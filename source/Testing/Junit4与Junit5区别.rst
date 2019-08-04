Junit4与Junit5区别
===================

*  `Exception Handling`_
*  `Annotation`_


Exception Handling
----------------------

Junit5

.. code-block:: java

  @Test
  void shouldThrowException() throws Exception {
      Task task = buildTask();
      LocalDateTime oneHourAgo = LocalDateTime.now().minusHours(1);
      assertThrows(IllegalArgumentException.class,
            () -> task.execute(oneHourAgo));
  }
  
Junit4

.. code-block:: java

  @Test(expected = IllegalArgumentException.class)
  public void shouldThrowException() throws Exception {
      Task task = buildTask();
      LocalDateTime oneHourAgo = LocalDateTime.now().minusHours(1);  task.execute(oneHourAgo);
  }


Annotation
------------

.. image:: ../../images/diff45.png
  :width: 500px


Remark
----------

* 务必不要在同一个测试里面尝试同时使用两种版本

.. index:: Testing

