3. Jest for Async
============================


Using callback
-----------------------

假设, fetchData是一个异步的方法(如里面会调用axios)，fetchData(callback)触发调用，当完成时候会调用callback。

Wrong way
^^^^^^^^^^^^^^^

.. code-block:: javascript
  
  test('the data is peanut butter', () => {
    function callback(data) {
      expect(data).toBe('this is the expect value');
    }
    fetchData(callback);
  }); 

在这种写法中，fetchData(callback)触发后，测试就会完结，并不会等到它真正的fetch结束并进入callback.

Correct way
^^^^^^^^^^^^^^^^^^^

.. code-block:: javascript
  :linenos:
  :emphasize-lines: 1,4
  
  test('the data is peanut butter', done => {
    function callback(data) {
      expect(data).toBe('this is the expect value');
      done();
    }
    fetchData(callback);
  });

