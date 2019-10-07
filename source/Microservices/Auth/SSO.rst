Single Sign On
======================

SSO within same domain
----------------------------

假设有app1.comp.com, app2.comp.com, app3.comp.com希望共享登陆

比如使用Session+Cookie的方式：
* 在前端只需要设置Domain为comp.com,这三者因为属于同一个domain下，所以可以分享cookie获取共同的用户资料。
* 而后台那边就需要连接到同一个session管理，比如由一个统一的服务统治下的Redis。

设想过程如下：
* 用户触发app1.comp.com, app1后台校验cookie里的token/用户信息，如果有则发送都sso服务进行验证


SSO cross different domains
-------------------------------

假设有app1.comp1.com, app2.comp2.com, app3.comp3.com希望共享登陆，因为他们跨域，所以没办法共享同一个cookie.

所以这里首先需要解决的是，在其中一个域登陆之后，怎么通知其他域知道这个登陆了的信息和获取相关的用户信息


流程
---------

初始A，B，C全部处于未登录状态，没有令牌(tokenid)
  
1. 用户访问A系统，没有令牌，A系统验证未登录，直接返回A的登录界面；
2. A输入账号密码进行登录请求到SSO中（附带重定向url）；
3. 然后SSO验证用户信息正确，生成身份令牌，将身份令牌转为jwt，将用户信息使用AES加密，将jwt身份令牌和用户信息作为键值对存进redis，时间设为30分钟，将身份令牌种进cookie，重定向回携带的url（到达了子系统）；
4. 子系统发现有身份令牌，发送请求到达SSO验证令牌真伪；
5. SSO收到验证请求，验证通过，返回AES加密的用户信息；
6. 子系统收到通过信息，将用户信息解密，存进session一分钟(token)，跳转页面至首页或用户请求页；
7. 一分钟失效后或者子系统访问时，发现有令牌但session中无信息，再次重复4,5,6。






.. index:: Security, SSO
