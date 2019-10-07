OAuth
============

基础概念
^^^^^^^^

* http://www.ruanyifeng.com/blog/2019/04/oauth_design.html

OAuth的四种方式
^^^^^^^^^^^^^^^^

无论那种方式，用户都需要先注册获取AppId/AppSecret才可能换取token： http://www.ruanyifeng.com/blog/2019/04/oauth-grant-types.html

* **authorization code**：response_type=code
* **implicit**： response_type=token
* **password**： grant_type=password
* **client credentials**： grant_type=client_credentials


OAuth with Spring
^^^^^^^^^^^^^^^^^^^^^

* http://blog.huotaihe.com/2019/06/21/spring-boot-oauth2/

.. index:: OAuth, Security, Spring
