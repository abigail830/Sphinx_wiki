JWS、Token认证
===================

* `JWT Basic`_

  * `Header`_
  * `Payload`_
  * `Signature`_

* `Authentication`_
* `Reference`_


``````````````````````


JWT Basic
------------

JWT 的全称是 JSON Web Token. 由三部分组成: Header.Payload.Signature 

Header
^^^^^^^^^^

* alg: algorithm, 默认是 HMAC SHA256
* 在传输前会对内容进行Base64Url转换

.. code-block:: json
  
  {
    "alg": "HS256", 
    "typ": "JWT" 
  }

Payload
^^^^^^^^^^

.. code-block:: json
  
  {
    "sub": "1234567890",
    "name": "John Doe",
    "admin": true
  }


默认field(也可以添加私有field，如上),在传输前会对内容进行Base64Url转换

* iss (issuer)：签发人
* exp (expiration time)：过期时间
* sub (subject)：主题
* aud (audience)：受众
* nbf (Not Before)：生效时间
* iat (Issued At)：签发时间
* jti (JWT ID)：编号


Signature
^^^^^^^^^^^^^^

根据Header中指定的算法，对Header和Payload进行加密，然后把Base64Url转换后的header.payload.signature并合为JWT。



Authentication
-----------------------

这方法可以应对Cookie、Session认证时提到的两个主要问题:

* 作为cookie的替换，可以把生成的JWT保存的如sessionStorage。
* 为服务器端无状态，把JWT 3个部分都整体的发送并保存在客户端，客户端再访问的时候又原样带回来做验证。


Reference
-------------

* JWT Debugger tools: https://jwt.io/
* Demo: https://dzone.com/articles/spring-boot-security-json-web-tokenjwt-hello-world

.. index:: Authentication

