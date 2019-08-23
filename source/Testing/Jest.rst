Jest Basic
================

* `Basic Notes`_
* `Basic Test Example with Javascript`_
* `Mock With Jest`_
* `Stub With Jest`_
* `Mock module - such as 3rd party lib`_
* `async, await and spyOn`_

Basic Notes
-----------------
* 命名：如果代码是abc.js,测试代码命名为abc.test.js
* 和Junit一样，拥有beforeAll/beforeEach/afterEach/afterAll
* TestSuit可以用describe包起来，里面每个案例以test或it开头
* 每个测试案例的描述可以参考“should do sth when condition fulfills"
* Jest 官网： https://jestjs.io/docs/en/getting-started

.. code-block:: javascript
  
  beforeAll(() => console.log('1 - beforeAll'));
  beforeEach(() => console.log('1 - beforeEach'));
  test('test1', () => console.log('1 - test'));
  test('test2 ', () => console.log('1 - test'));
  afterEach(() => console.log('1 - afterEach'));
  afterAll(() => console.log('1 - afterAll'));
  
  

Basic Test Example with Javascript
---------------------------------------

Sum.js

.. code-block:: javascript
  
  function sum(a, b){
    return a+b;
  }
  module.exports = sum;

Sum.test.js

.. code-block:: javascript
  
  const sum = require('../src/sum');

  describe("test add function", ()=>{

    test('should equals 3 when 1+2', ()=>{
      //given

      //when
      var result = sum(1,2);
      //then
      expect(result).toBe(3);
    })
  })
  
Mock With Jest
------------------------------

filter.js

.. code-block:: javascript
  
  function filter(items, callback){
    for(let index=0;index<items.length;index++){
        callback(items[index]);
    }
  }
  module.exports = filter;

filter.test.js

* 使用jest.fn封装mock object，可后续进行验证
* mockFilterCallback.mock.calls.length， 被mock的object被调用次数
* mockFilterCallback.mock.calls[0][0]， 被mock的object第一次被调用时候第一个入参
* mockFilterCallback.mock.results[0].value 被mock的object第一次被调用时的结果
* mockFilterCallback.mock.instances.length 被mock的object被初始化的次数


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
  
  
Stub With Jest
---------------------------------
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


Mock module - such as 3rd party lib
------------------------------------------------

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

async, await and spyOn
-------------------------------

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

* 思路一: 单独测试fetch.js的时候，mock axios module, 可以验证callback方法的确被调用了

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
  
* 思路二: 对于event.js来说，可以验证mock了的fetch方法的确被调用了，此时“fetchPostsList be called!”并不会被打印，因为没有使用真身

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
  

* 思路三: 对于event.js来说，可以验证fetch方法的确被调用了，spy的fetch因为并没有override其中的implementation其实是真身，所以会打印“fetchPostsList be called!”

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

