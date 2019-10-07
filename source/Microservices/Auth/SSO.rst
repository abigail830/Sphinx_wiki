Single Sign On
======================

SSO within same domain
----------------------------

假设有app1.comp.com, app2.comp.com, app3.comp.com希望共享登陆

比如使用Session+Cookie的方式：
* 在前端只需要设置Domain为comp.com,这三者因为属于同一个domain下，所以可以分享cookie获取共同的用户资料。
* 而后台那边就需要连接到同一个session管理，比如由一个统一的服务统治下的Redis。


SSO cross different domains
-------------------------------

假设有app1.comp1.com, app2.comp2.com, app3.comp3.com希望共享登陆，因为他们跨域，所以没办法共享同一个cookie.

**实现一**

* 用户访问app1.comp1.com，app1后端收到请求开始校验cookie，如果有token存在，则发送到统一的sso服务进行验证。如sso.com/sso/authcookies
* 若验证通过，app1可以进行后续操作，如果验证不通过，则跳转到app1登陆页面app1.comp1.com/login
* app1登陆页面会进一步把用户名和密码POST sso.com/sso请求登陆验证，如果成功，sso服务则返回token（JWT或OAuth）及所有需要参与SSO的域名列表
* app1前端收到之后把token存到cookie，并触发SSO域名列表下相关域名cookie的更新（如触发app2.comp2.com/addcookie，app3.comp3.com/addcookie）





.. index:: Security, SSO
