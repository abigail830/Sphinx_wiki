Jest Basic
==================

* `Basic Notes`_
* `Basic Test Example with Javascript`_
* 

Basic Notes
-----------------
* 命名：如果代码是abc.js,测试代码命名为abc.test.js
* 和Junit一样，拥有beforeAll/beforeEach/afterEach/afterAll
* TestSuit可以用describe包起来，里面每个案例以test或it开头
* 每个测试案例的描述可以参考“should do sth when condition fulfills"
* Jest 官网： https://jestjs.io/docs/en/getting-started

.. code-block:: javascript
  
  beforeAll(() => console.log('beforeAll'));
  beforeEach(() => console.log('beforeEach'));
  test('test1', () => console.log('1 - test'));
  test('test2 ', () => console.log('2 - test'));
  afterEach(() => console.log('afterEach'));
  afterAll(() => console.log('afterAll'));
  
  

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


Coverage
-------------

Jest优势之一就是已经集成了istanbul可以生成覆盖率报告。需要在jest.config.js中配置：

.. code-block:: javascript
  
  module.exports = {
    collectCoverage: true,
    coverageDirectory: './coverage'
  }

报告默认就包括了4个维度的覆盖率数据：

* 行覆盖率（line coverage）：是否每一行都执行了？
* 函数覆盖率（function coverage）：是否每个函数都调用了？
* 分支覆盖率（branch coverage）：是否每个if代码块都执行了？
* 语句覆盖率（statement coverage）：是否每个语句都执行了？




.. index: Testing, Jest
