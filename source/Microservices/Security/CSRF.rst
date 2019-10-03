CSRF
==========


Example
--------------------------

假设有转账接口：

.. code-block::

  POST /transfer HTTP/1.1
  Host: bank.example.com
  Cookie: JSESSIONID=randomid; Domain=bank.example.com; Secure; HttpOnly
  Content-Type: application/x-www-form-urlencoded
  
  amount=100.00&routingNumber=1234&account=9876


如果有个钓鱼网站使用隐藏form的方式，当点击“Win Money"的时候，隐藏form就会自动提交

.. code-block:: html
  
  <form action="https://bank.example.com/transfer" method="post">
    <input type="hidden" name="amount" value="100.00"/>
    <input type="hidden" name="routingNumber" value="evilsRoutingNumber"/>
    <input type="hidden" name="account" value="evilsAccountNumber"/>
    <input type="submit" value="Win Money!'/>
  </form>

To protect against CSRF attacks we need to ensure there is something in the request that the evil site is unable to provide.



.. index:: Security, SpringBoot
