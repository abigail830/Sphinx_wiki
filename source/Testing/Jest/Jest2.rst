2. Mock、Stub、Spy of Jest 
==============================

* `Mock - jest.mock`_
* `Stub - jest.fn()`_
* `Spy - jest.spyOn`_
  
Mock - jest.mock
------------------------------

* jest.mock('module path'), 如jest.mock('axios');
* mockImplementation()/mockImplementationOnce(), 如 axios.get.mockImplementation(() => Promise.resolve(resp)),这里就不是直接模拟返回值，而是模拟这个方法体的内部操作。
* mockRejectedValue(new Error()),模拟async里面的catch异常场景。等效于mockImplementation(() => Promise.reject())，也等效于mockImplementation(() => throw new Error())

Mock axios - user.js

.. code-block:: javascript
  
  import axios from 'axios';

  export default class Users {
    static all() {
      return axios.get('/users.json').then(resp => resp.data);
    }
  }

user.test.js

.. code-block:: javascript
  
  import axios from 'axios';
  import Users from '../src/users';

  jest.mock('axios');

  test('should fetch users', () => {
    const users = [{name: 'Bob'}];
    const resp = {data: users};

    axios.get.mockResolvedValue(resp);
    // or you could use the following depending on your use case:
    // axios.get.mockImplementation(() => Promise.resolve(resp))

    return Users.all().then(data => {
      expect(data).toEqual(users);
      expect(axios.get).toHaveBeenCalled();
    })
  });

Stub - jest.fn()
---------------------------------

filter.js

.. code-block:: javascript
  
  function filter(items, callback){
    for(let index=0;index<items.length;index++){
        callback(items[index]);
    }
  }
  module.exports = filter;


* 使用jest.fn封装mock object，等于带上了窃听器，可后续被进一步验证。
* jest.fn()里面参数可以是空，这代表空白替身，没有具体功能但能被验证。
* jest.fn()也可以传一个实际的方法， 如jest.fn(x=>(x%2)==0),这代表一个被窃听中的具体方法。

验证方法
``````````
* expect(mockFunc).toBeCalled();
* expect(mockFunc).toBeCalledWith(arg1, arg2);
* expect(mockFunc).lastCalledWith(arg1, arg2);

更多：（具体可参考：https://jestjs.io/docs/zh-Hans/expect.html）

* mockFilterCallback.mock.calls.length， 被mock的object被调用次数
* mockFilterCallback.mock.calls[0][0]， 被mock的object第一次被调用时候第一个入参
* mockFilterCallback.mock.results[0].value 被mock的object第一次被调用时的结果
* mockFilterCallback.mock.instances.length 被mock的object被初始化的次数

方式一.带侦听的真function
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

filter.test.js

.. code-block:: javascript
  
  //production code
  const filter = require('../src/filter');

  //test code
  describe("TestSuit for filter", ()=>{
    it("verify callback of filter should be invoke correctly", ()=>{
        //given
        const mockFilterCallback = jest.fn(x=>(x%2)==0);
        //when
        filter([1,2,3], mockFilterCallback);
        //then
        expect(mockFilterCallback.mock.calls.length).toBe(3);
        expect(mockFilterCallback.mock.calls[0][0]).toBe(1);
        expect(mockFilterCallback.mock.calls[1][0]).toBe(2);
        expect(mockFilterCallback.mock.calls[2][0]).toBe(3);
        expect(mockFilterCallback.mock.instances.length).toBe(3)

        expect(mockFilterCallback.mock.results[0].value).toBeFalsy;
        expect(mockFilterCallback.mock.results[1].value).toBeTruthy;
        expect(mockFilterCallback.mock.results[2].value).toBeFalsy;
    })
  })

方式二. 空白的纯侦听木偶
^^^^^^^^^^^^^^^^^^^^^^^^^^

* jest.fn()封装mock object
* mockReturnValueOnce，模拟返回结果，只会根据设置次序返回一次
* mockReturnValue 模拟返回结果，每次调用都会使用


.. code-block:: javascript

  const filter = require('../src/filter');

  //test code
  describe("test suit for filter", ()=>{

    test("test case1", ()=>{
        //given
        const mockFilterCallback = jest.fn();
        mockFilterCallback
            .mockReturnValueOnce(true)
            .mockReturnValueOnce(true)
            .mockReturnValue(false);
        //when
        filter([1,2,3], mockFilterCallback);
        //then
        expect(mockFilterCallback.mock.calls.length).toBe(3);
        expect(mockFilterCallback.mock.calls[0][0]).toBe(1);
        expect(mockFilterCallback.mock.calls[1][0]).toBe(2);
        expect(mockFilterCallback.mock.calls[2][0]).toBe(3);
        expect(mockFilterCallback.mock.instances.length).toBe(3)
        expect(mockFilterCallback.mock.results[0].value).toBeTruthy;
        expect(mockFilterCallback.mock.results[1].value).toBeTruthy;
        expect(mockFilterCallback.mock.results[2].value).toBeFalsy;
    })
  })
  

Spy - jest.spyOn
-------------------------------


**例子： event.js 调用 fetch 调用 axios**

event.js

.. code-block:: javascript

  import fetch from './fetch';
  export default {
    async getPostList() {
      return fetch.fetchPostsList(data => {
        // do something
        console.log('fetchPostsList be called!');
      });
    }
  }

fetch.js

.. code-block:: javascript  
  
  import axios from 'axios';

  export default {
    async fetchPostsList(callback) {
      return axios.get('https://jsonplaceholder.typicode.com/posts').then(res => {
        return callback(res.data);
      })
    }
  }

* **思路一:** 单独测试fetch.js的时候，mock axios module, 可以验证callback方法的确被调用了

.. code-block:: javascript  
  
  import fetch from '../src/fetch.js'

  test('should able to invoke the callback via fetchPostsList', async () => {
    expect.assertions(1);
    //given
    let mockFn = jest.fn();
    //when
    await fetch.fetchPostsList(mockFn);
    // then
    expect(mockFn).toBeCalled();
  })
  
* **思路二:** 对于event.js来说，同理可以验证mock了的fetch方法的确被调用了，此时“fetchPostsList be called!”并不会被打印，因为没有使用真身

.. code-block:: javascript  
  
  import events from '../src/events';
  import fetch from '../src/fetch';

  jest.mock('../src/fetch.js');
  // jest.mock('../src/fetch.js', () => ({ fetch: jest.fn() }))

  test('mock 整个 fetch.js模块', async () => {
    //fetch.fetchPostsList.mockResolvedValue("abc");
    expect.assertions(2);
    //when
    await events.getPostList();
    //then
    expect(fetch.fetchPostsList).toHaveBeenCalled();
    expect(fetch.fetchPostsList).toHaveBeenCalledTimes(1);
  });
  

* **思路三:** 对于event.js来说，spyOn fetch.fetchPostsList, 可以验证fetch方法的确被调用了，spy的fetch因为并没有override其中的implementation其实是真身，所以会打印“fetchPostsList be called!”

.. code-block:: javascript  
  
  import events from '../src/events';
  import fetch from '../src/fetch';
  
  test('使用jest.spyOn()监控fetch.fetchPostsList被正常调用', async() => {
    expect.assertions(2);
    //given
    const spyFn = jest.spyOn(fetch, 'fetchPostsList');
    //when
    await events.getPostList();
    //then
    expect(spyFn).toHaveBeenCalled();
    expect(spyFn).toHaveBeenCalledTimes(1);
  })


.. index:: Testing

