About Jest Basic
================

* `Basic Notes`_
* `Basic Test Example with Javascript`_
* `Mock With Jest`_
* `Stub With Jest`_

Basic Notes
-----------------
* 命名：如果代码是abc.js,测试代码命名为abc.test.js
* 和Junit一样，拥有beforeAll/beforeEach/afterEach/afterAll
* TestSuit可以用describe包起来，里面每个案例以test或it开头
* 每个测试案例的描述可以参考“should do sth when condition fulfills"

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
 
