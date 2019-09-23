0. Jasmine Basic
=====================

* `命名 & 常见要素`_
* `入门例子`_
* `Matchers匹配符`_
* `运行`_

命名 & 常见要素
------------------

* 测试文件命名：[被测类名].spec.ts
* describe: 测试集，里面包含多个测试案例， 描述可为‘[被测类] Test’ 或更具体的内容
* it/test: 测试案例，‘should do sth when condition fulfills'
* 每个测试案例内以given/when/then的套路进行
* describe中可以包含多个it, 也可以内嵌套另一个describe
* beforeAll/beforeEach/afterAll/afterEach
* 当希望忽略跳过某个测试/测试集，在前面加‘x'. 如'xdescribe/xit'


入门例子
---------

**sum.js**

.. code-block:: javascript
  
  function sum(a, b){
    return a+b;
  }
  module.exports = sum;
  

**sum.spec.js**

.. code-block:: javascript
  
  const sum = require('../src/sum');

  describe("test sum function", ()=>{
  
    it('should equals 3 when 1+2', ()=>{
      //given
  
      //when
      var result = sum(1,2);
      //then
      expect(result).toBe(3);
    })
  })


Matchers匹配符
------------------

More matchers could refer to https://jasmine.github.io/api/3.3/matchers.html

.. code-block:: typescript
  
  expect(true).toBe(true);
  expect(false).not.toBe(true);
  expect(12).not.toBe(null);
  expect(object1).toEqual(object2);
  expect(null).toBeNull();
  expect(foo).toBeTruthy();
  expect(a).not.toBeTruthy();
  expect(2.78).toBeLessThan(pi);
  expect(pi).toBeGreaterThan(2.78);

  var a = ["foo", "bar", "baz"];
  expect(a).toContain("bar");
  
  var message = "foo bar baz";
  expect(message).toMatch(/bar/);
  expect(message).toMatch("bar");
  expect(message).not.toMatch(/quux/);
  
  var a = {
      foo: "foo"
  };  
  expect(a.foo).toBeDefined();
  expect(a.bar).not.toBeDefined();

运行
----------

在Cli创建的Angular项目中，‘npm test'/'yarn test'就可以运行测试案例. 因为angular默认也使用了karma的chrome驱动，所以运行时会弹出chrome浏览器



.. index:: Angular, Jasmine, Testing
