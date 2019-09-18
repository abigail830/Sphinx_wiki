Jasmine Basic
=====================

Same example as Jest still valid in Jasmine.

**sum.js**

.. code-block:: javascript
  
  function sum(a, b){
    return a+b;
  }
  module.exports = sum;
  

**sum.spec.js**

.. code-block:: javascript
  
  const sum = require('../src/sum');

  describe("test add function", ()=>{
  
    it('should equals 3 when 1+2', ()=>{
      //given
  
      //when
      var result = sum(1,2);
      //then
      expect(result).toBe(3);
    })
  })

Also, Jasmine also have the beforeEach/afterEach/beforeAll/afterAll.

Only different is, when we want to skip test suit or test case, we would add 'x' in the front. Said 'xit'/'xdescribe'

Matchers
^^^^^^^^^^^^^

.. code-block:: typescript
  
  expect(true).toBe(true);
  expect(false).not.toBe(true);
  expect(12).not.toBe(null);
  expect(object1).toEqual(object2);
  expect(null).toBeNull();

  var message = "foo bar baz";
  expect(message).toMatch(/bar/);
  expect(message).toMatch("bar");
  expect(message).not.toMatch(/quux/);
  
  var a = {
      foo: "foo"
  };  
  expect(a.foo).toBeDefined();
  expect(a.bar).not.toBeDefined();


