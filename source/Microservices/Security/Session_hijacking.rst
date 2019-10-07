Session hijacking 会话劫持
================================

From Baidu Wiki
------------------

https://baike.baidu.com/item/%E4%BC%9A%E8%AF%9D%E5%8A%AB%E6%8C%81


  例如你Telnet到某台主机，这就是一次Telnet会话；你浏览某个网站，这就是一次HTTP会话。
  
  而会话劫持（Session Hijack），就是结合了嗅探以及欺骗技术在内的攻击手段。例如，在一次正常的会话过程当中，攻击者作为第三方参与到其中，他可以在正常数据包中插入恶意数据，也可以在双方的会话当中进行监听，甚至可以是代替某一方主机接管会话。
  
我们可以把会话劫持攻击分为两种类型：

* 中间人攻击(Man In The Middle，简称MITM)，
* 注射式攻击（Injection）；

并且，还可以把会话劫持攻击分为两种形式：

* 被动劫持，
* 主动劫持；


预防&检测
-----------

* 网络层面限制和校验入网连接
* 使用加密通信

  * 使用Https
  * 使用可靠的cert


Reference
---------------

* https://www.cnblogs.com/baibaomen/p/http-session-hijack.html


.. index:: Security, 
