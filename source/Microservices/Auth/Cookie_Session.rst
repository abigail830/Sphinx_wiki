Cookie、Session认证
==========================

HTTP Cookie
---------------
（也叫Web Cookie或浏览器Cookie）是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。

**Cookie key elements：**

* Name/Value
* Domain
* HttpOnly
* Size: Max 5M

.. image:: ../../images/cookie.png
  :width: 500px
  

Alternative
^^^^^^^^^^^^^^^
由于服务器指定Cookie后，浏览器的每次请求都会携带Cookie数据，会带来额外的性能开销（尤其是在移动环境下）。新的浏览器API已经允许开发者直接将数据存储到本地，如使用 `Web Storage`_ API （本地存储和会话存储）或 IndexedDB 。


**Web Storage**

https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Storage_API

* **Window.sessionStorage** 为每一个给定的源（given origin）维持一个独立的存储区域，该存储区域在页面会话期间可用（即只要浏览器处于打开状态，包括页面重新加载和恢复）。
* **Window.localStorage** 同样的功能，但是在浏览器关闭，然后重新打开后数据仍然存在。
* 从Firefox 45开始，当浏览器崩溃或重启时，每个源的存储大小将限制在10M，以避免因过度使用web storage引起的内存问题

**Indexed DB**

Refer: https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API


Authentication认证
--------------------------

.. image:: ../../images/cookie_auth.png
  :width: 400px





.. index:: Cookie, Authentication, Microservices
 
