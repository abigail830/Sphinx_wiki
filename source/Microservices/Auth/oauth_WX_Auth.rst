Oauth - Wechat Auth Example
==============================

授权认证过程
-----------------

* `Step1. 小程序中调用wx.login获取code`_
* `Step2. 拿到授权码调用自己的后台应用`_
* `Step3. 后台应用获取授权码再进一步调用微信的接口`_
* `Step4. 小程序在接收到的结果里面获取`_

授权后的使用
---------------

* `使用session_key和open_id获取用户信息`_
  
  * `小程序端`_
  * `后台程序解密`_



代码摘要
-----------


Step1. 小程序中调用wx.login获取code
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

wx.login已经封装好如何调用微信后台服务获取授权码，所以这一步比较简单

Step2. 拿到授权码调用自己的后台应用
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

如，小程序端把授权码放在header的“X-WX-Code”中，然后调用后台约定接口this.globalData.apiBase + "/common/wxLogin"

.. code-block:: javascript

  wx.login({
        success: res => {
          console.log(res);
          wx.setStorageSync('wx_code', res.code);
          const header = {
                "X-WX-Code": res.code
          };
          console.log("登录中...");
          // 发送 res.code 到后台换取 openId, sessionKey, unionId
          wx.request({
            url: this.globalData.apiBase + "/common/wxLogin",
            method: "GET",
            header: header,
            dataType: "json",
            complete: res => {
              wx.hideLoading();
            },
            success: resolve,

            fail: reject,
          });
        }
      });
    });

Step3. 后台应用获取授权码再进一步调用微信的接口
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

在后台接口中，预设好AppID和AppSecret，在获取刚存在header中的授权码一起，调用微信提供的auth接口https://api.weixin.qq.com/sns/jscode2session?

.. code-block:: java
  
  @GetMapping(value = "/wxLogin", produces = MediaType.APPLICATION_JSON_UTF8_VALUE)
	@ResponseBody
	public String login(HttpServletRequest request) {
		String code = request.getHeader("X-WX-Code");
		String loginResult =  HttpClientUtil.instance().getData(
				"https://api.weixin.qq.com/sns/jscode2session?appid=" + appId +
						"&secret=" + appSecret +
						"&grant_type=authorization_code" +
						"&js_code=" + code);
		logger.info("LoginResult: {}", loginResult);
		return loginResult;
	}

Step4. 小程序在接收到的结果里面获取
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

如果后台登陆成功，小程序会得到对应的session_key和open_id.至此，登陆授权认证步骤初步完成

.. code-block:: javascript

  return new Promise((resolve, reject) => {
    wx.login({
      //continue from promise of step2
      });
  }).then((result) => {
      wx.setStorageSync('skey', result.data.session_key);
      wx.setStorageSync('openid', result.data.openid);
      console.log("登录后台成功");
      // util.showSuccess('登录后台成功');
      console.log(this.globalData);
      this.globalData.authInfo.skey = result.data.session_key;
      this.globalData.authInfo.openid = result.data.openid;

    //other operation
  }, (reject) => {
      util.showModel('登录后台错误', reject.errMsg)
  });


使用session_key和open_id获取用户信息
-------------------------------------

小程序端
^^^^^^^^^^

小程序端借用已经封装好的wx.getSetting， wx.getUserInfo，带上之前授权获取的session_key，预设好的appId,调用后台获取信息并解密。

.. code-block:: javascript
  
  wx.getSetting({
    success: res2 => {
          if (res2.authSetting['scope.userInfo']) {
            // 已经授权，可以直接调用 getUserInfo 获取头像昵称，不会弹框
            wx.getUserInfo({
              success: res3 => {
                console.log(res3);
                if (res3.iv && res3.encryptedData) {
                  wx.request({
                    url: this.globalData.apiBase + "/common/decrypt",
                    method: "GET",
                    header: {
                      iv: res3.iv,
                      encryptedData: res3.encryptedData,
                      appId: "wxd7b407ad92867db4",
                      skey: result.data.session_key
                    },
                    dataType: "json",
                    complete: res => {
                      wx.hideLoading();
                    },
                    success: (result2) => {
                    },
                    fail: (result2) => {
                      util.showModel('登录后台错误', result2.msg)
                    },
                  });
                }
                // 可以将 res 发送给后台解码出 unionId
                this.globalData.userInfo = res3.userInfo;

                // 由于 getUserInfo 是网络请求，可能会在 Page.onLoad 之后才返回
                // 所以此处加入 callback 以防止这种情况
                if (this.userInfoReadyCallback) {
                  this.userInfoReadyCallback(res3)
                }
              }
            })
          }
        }
      });

后台程序解密
^^^^^^^^^^^^^^^^

**Controller**

.. code-block:: java
  
  @GetMapping(value = "/decrypt", produces = MediaType.APPLICATION_JSON_UTF8_VALUE)
	@ResponseBody
	public String decrypt(HttpServletRequest request) {
		String skey = request.getHeader("skey");
		String encryptedData = request.getHeader("encryptedData");
		String iv = request.getHeader("iv");
		WXBizDataCrypt biz = new WXBizDataCrypt(appId, skey);
		return biz.decryptData(encryptedData, iv);
	}

**WXBizDataCrypt.java**

* 其中session_id，encryptedData和iv这些信息都是Base64编码了，所以要先解码
* 解码后通过AES的方式解密
* 这里还设计了使用watermark的方式再把解密的结果对照watermark做验证

.. code-block: java
  
  public class WXBizDataCrypt {
    private static final Logger logger = LoggerFactory.getLogger(WXBizDataCrypt.class);
	  private String appid;
	  private String sessionKey;
  
	  public WXBizDataCrypt(String appid, String sessionKey) {
		  this.appid = appid;
  		this.sessionKey = sessionKey;
	  }
  
	  public String decryptData(String encryptedData, String iv) {
		  if (StringUtils.length(sessionKey) != 24) {
			  return "ErrorCode::$IllegalAesKey;";
  		}
	  	// 对称解密秘钥 aeskey = Base64_Decode(session_key), aeskey 是16字节。
		  byte[] aesKey = Base64.decodeBase64(sessionKey);
  
	  	if (StringUtils.length(iv) != 24) {
		  	return "ErrorCode::$IllegalIv;";
  		}
	  	// 对称解密算法初始向量 为Base64_Decode(iv)，其中iv由数据接口返回。
  		byte[] aesIV = Base64.decodeBase64(iv);
  
	  	// 对称解密的目标密文为 Base64_Decode(encryptedData)
		  byte[] aesCipher = Base64.decodeBase64(encryptedData);
  
	  	Map<String, Object> map = new HashMap<>();
  
	  	try {
		  	byte[] resultByte = AESUtils.decrypt(aesCipher, aesKey, aesIV);

			  if (null != resultByte && resultByte.length > 0) {
				  String userInfo = new String(resultByte, "UTF-8");
  				logger.info("UserInfo: {}", userInfo);
	  			map.put("code", "0000");
		  		map.put("msg", "succeed");
			  	map.put("userInfo", JsonUtil.toObject(userInfo, Map.class));

				  // watermark参数说明：
  				// 参数  类型  说明
	  			// watermark   OBJECT  数据水印
		  		// appid   String  敏感数据归属appid，开发者可校验此参数与自身appid是否一致
			  	// timestamp   DateInt 敏感数据获取的时间戳, 开发者可以用于数据时效性校验'
  
	  			// 根据微信建议：敏感数据归属appid，开发者可校验此参数与自身appid是否一致
		  		// if decrypted['watermark']['appid'] != self.appId:
			  	JsonObject jsons = new JsonParser().parse(userInfo).getAsJsonObject();
				  String id = jsons.getAsJsonObject("watermark").get("appid").getAsString();
  				if (!StringUtils.equals(id, appid)) {
	  				return "ErrorCode::$IllegalBuffer;";
		  		}
  			} else {
	  			map.put("status", "1000");
		  		map.put("msg", "false");
  			}
	  	} catch (InvalidAlgorithmParameterException | UnsupportedEncodingException e) {
		  	e.printStackTrace();
  		}
  
	  	  return JsonUtil.toJson(map);
	    }
  }

**AESUtil**
^^^^^^^^^^^^^^

.. code-block:: java
  
  public class AESUtils {
	  public static boolean initialized = false;
  	/**
	   * AES解密
  	 *  
	   * @param content 密文
  	 * @return
	   * @throws InvalidAlgorithmParameterException
  	 * @throws NoSuchProviderException
	   */
    public static byte[] decrypt(byte[] content, byte[] keyByte, byte[] ivByte)
	  		throws InvalidAlgorithmParameterException {
		  initialize();
  		try {
	  		Cipher cipher = Cipher.getInstance("AES/CBC/PKCS7Padding");
		  	Key sKeySpec = new SecretKeySpec(keyByte, "AES");
  
	  		cipher.init(Cipher.DECRYPT_MODE, sKeySpec, generateIV(ivByte));// 初始化
		  	return cipher.doFinal(content);
  		} catch (NoSuchAlgorithmException | NoSuchPaddingException | IllegalBlockSizeException
		  		| InvalidKeyException | NoSuchProviderException | BadPaddingException e) {
	  		e.printStackTrace();
  		} catch (Exception e) {
	  		e.fillInStackTrace();
		  	e.printStackTrace();
  		}
	  	return null;
  	}
  
	  public static void initialize() {
		  if (initialized) return;
  		Security.addProvider(new BouncyCastleProvider());
	  	initialized = true;
  	}

	  /**
  	 * 生成iv
	   *
  	 * @param iv
	   * @return
  	 * @throws Exception
	   * @see
  	 */
	  public static AlgorithmParameters generateIV(byte[] iv)
		  	throws Exception {
  		AlgorithmParameters params = AlgorithmParameters.getInstance("AES");
	  	params.init(new IvParameterSpec(iv));
  		return params;
	  }
  }


.. index:: Security, Wechat

