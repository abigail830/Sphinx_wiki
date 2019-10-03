XSS 跨站脚本攻击
====================

From Wiki: https://zh.wikipedia.org/wiki/%E8%B7%A8%E7%B6%B2%E7%AB%99%E6%8C%87%E4%BB%A4%E7%A2%BC


跨站脚本（英语：Cross-site scripting，通常简称为：XSS）是一种网站应用程序的安全漏洞攻击，是代码注入的一种。它允许恶意用户将代码注入到网页上，其他用户在**观看网页时就会受到影响**。这类攻击通常包含了HTML以及用户端脚本语言。

XSS攻击通常指的是通过利用网页开发时留下的漏洞，通过巧妙的方法注入恶意指令代码到网页，使用户加载并执行攻击者恶意制造的网页程序。这些恶意网页程序通常是JavaScript，但实际上也可以包括Java，VBScript，ActiveX，Flash或者甚至是普通的HTML。攻击成功后，攻击者可能得到更高的权限（如执行一些操作）、私密网页内容、会话和cookie等各种内容。

思考
-------

这里担心的是客户在向后台获取资料的时候被塞进了恶意的内容，所以解决思路是更严格的校验获取到的内容

比如对于nodes前端，参考： https://github.com/validatorjs/validator.js



.. index:: Security, XSS

