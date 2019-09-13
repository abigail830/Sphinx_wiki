1. Jest Basic
==================

* `Basic Notes`_
* `Basic Test Example with Javascript`_
* `Matcher`_
* `More example`_

Basic Notes
-----------------
* 命名：如果代码是abc.js,测试代码命名为abc.test.js或abc.spec.js
* 和Junit一样，拥有beforeAll/beforeEach/afterEach/afterAll
* TestSuit可以用describe包起来，里面每个案例以test或it开头
* describe.skip/test.skip/it.skip表示跳过某个测试集/测试，describe.only表示只运行一次
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

Matcher
-------------

在检验过程中，Jest提供了很多匹配的方式

.. code-block:: javascript

  test('demo of null', () => {
    // when
    const n = null;
    // then
    expect(n).toBeNull();
    expect(n).toBeDefined();
    expect(n).not.toBeUndefined();
    expect(n).not.toBeTruthy();
    expect(n).toBeFalsy();
  });
  
  test('demo of zero', () => {
    // when
    const z = 0;
    // then
    expect(z).not.toBeNull();
    expect(z).toBeDefined();
    expect(z).not.toBeUndefined();
    expect(z).not.toBeTruthy();
    expect(z).toBeFalsy();
  });

Common assertion
``````````````````````

* **toBeGreaterThan** expect(4).toBeGreaterThan(3);
* **toBeGreaterThanOrEqual** expect(4).toBeGreaterThanOrEqual(3.5);
* **toBeLessThan** expect(4).toBeLessThan(5);
* **toBeLessThanOrEqual** expect(4).toBeLessThanOrEqual(4.5);
* **toBe** expect(value).toBe(4);
* **toEqual**  expect(value).toEqual(4);
* **toMatch** expect('team').not.toMatch(/I/);
* **toContain** expect(shoppingList).toContain('beer');

More matcher pls refer to : `Jest official website <https://jestjs.io/docs/en/expect.html#expectanything>`_


To assert the exception
``````````````````````````

* expect(compileAndroidCode).toThrow();
* expect(compileAndroidCode).toThrow(ConfigError);
* expect(compileAndroidCode).toThrow('you are using the wrong JDK');


To assert the mock
``````````````````````

* **toHaveBeenCalled** expect(mockFn).toHaveBeenCalled()

(More could be mentioned in another doc talking about `Mock/Stub/Spy <http://wiki.saraqian.cn/Testing/Jest/Jest2.html>`_ )

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

More example
--------------------

For example, here is the source code of error.js:

.. code-block:: javascript
 
  export default function getErrorMessage(code) {
    if (code === 1) {
      return "The camel walks on a leg";
    } else if (code === 2) {
      return "Rabbits don't eat carrots";
    } else if (code === 3) {
      return "Cats don't eat mouses";
    }
    throw new Error("No error messages for that code");
  }

error.test.js would be:

.. code-block:: javascript
  
  import getErrorMessage from "./error-message";
  
  describe("getErrorMessage", () => {
    it("returns camel message when code is 1", () => {
      expect(getErrorMessage(1)).toBe("The camel walks on a leg");
    });
  
    it("returns rabbit message when code is 2", () => {
      expect(getErrorMessage(2)).toBe("Rabbits don't eat carrots");
    });
  
    it("returns cat message when code is 3", () => {
      expect(getErrorMessage(3)).toBe("Cats don't eat mouses");
    });
  
    it("throws an error otherwise", () => {
      expect(() => getErrorMessage(4)).toThrow("No error messages for that code");
    });
  });



.. index: Testing, Jest
