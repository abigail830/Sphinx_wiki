3. Jest for Async
============================

* `Using callback`_
* `Using Promise`_
* `Using async/await`_


:假设:
  fetchData是一个异步的方法(如里面会调用axios)，fetchData(callback)触发调用，当完成时候会调用callback。


Using callback
-----------------------

Wrong way
^^^^^^^^^^^^^^^

.. code-block:: javascript
  
  test('demo of wrong way', () => {
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
  
  test('demo of callback', done => {
    function callback(data) {
      expect(data).toBe('this is the expect value');
      done();
    }
    fetchData(callback);
  });

如果 done 永远都不被调用，那么的测试将会失败.


Using Promise
-------------------

.. code-block:: javascript
  
  test('demo of promise', () => {
    expect.assertions(1);
    return fetchData().then(data => {
      expect(data).toBe('this is the expect value');
    });
  });
  
  test('the fetch fails with an error', () => {
    expect.assertions(1);
    return fetchData().catch(e => expect(e).toMatch('error'));
  });

"expect.assertions(1)" - 代表的是在当前的测试中至少有一个断言是被调用的，否则判定为失败。所以无论是验证成功情况或是异常情况，必须有expect语句。


Using async/await
----------------------

.. code-block:: javascript
  
  test('the data is peanut butter', async () => {
    expect.assertions(1);
    const data = await fetchData();
    expect(data).toBe('peanut butter');
  });

  test('the fetch fails with an error', async () => {
    expect.assertions(1);
    try {
      await fetchData();
    } catch (e) {
      expect(e).toMatch('error');
    }
  });




.. index:: Testing, Jest

