0. Testing framework
===========================

* `Why Testing with Frontend`_
* `Testing framework`_
* `Testing strategic`_


Why Testing with Frontend
------------------------------

* Confident / Remoral fear to change code
* Code Quality (Follow SRP / Better modular code / Understand what is my component doing)
* Documentation
* Developer happy wihtout 996


Testing framework
-----------------------

* **Jasmine**

  - BDD js测试框架, `官网 <https://jasmine.github.io/>`_

* **Jest**
  
  -  `Jest官网 <https://jestjs.io/docs/en/dynamodb>`_ , 大包围，包括了测试框架和断言库和mock等所需元素，只需要引入一个包就可以。
  - 内置istanbul可以直接生成coverage报告

* **Mocha** 
  
  - js测试框架，不包含断言库，所以通常搭配chai使用，`Mocha官网 <https://mochajs.org/>`_
  - 由describe/it组成，也有before/after/beforeEach/afterEach， it.skip为跳过
  - mocha --recursive -R markdown > spec.md可以把测试用例生成文档
  
* **Karma**

  - 测试运行器，包含各种驱动,允许在浏览器下运行测试.
  - Angular默认就是Jasmine+Karma (但其实另外弹出浏览器会比较慢）, 也可以搭配Mocha使用
  
* **Chai**: 断言库

.. code-block:: javascript

  import chai from 'chai';
  let expect = chai.expect;
  
  expect(4 + 5).to.be.equal(9);
  expect(false).to.not.be.ok;
  expect(foo).to.be.an.instanceof(Foo);
  expect([1,2,3]).to.include(2);
  expect([]).to.be.empty;
 
* Sinon: 用于Mock

Testing strategic
----------------------

We have another wiki talking about `vue with react <http://wiki.saraqian.cn/Testing/Jest%20with%20React.html>`_. There mentioned a triangle for my thought of testing strategic, we also mentioned some good/bad for that triangle. But actually there is also other thought on this point.

* Unit Test -> UI Test -> Snapshot Test
* Unit Test -> Snapshot Test -> E2E Test
* Unit Test -> Integration Test -> E2E Test
