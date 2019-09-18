1. Karma Basic
===========================================

**Default Karma config for open browser:**

.. code-block:: javascript
  
  plugins: [
      require('karma-jasmine'),
      require('karma-chrome-launcher'),
      require('karma-jasmine-html-reporter'),
      require('karma-coverage-istanbul-reporter'),
      require('@angular-devkit/build-angular/plugins/karma')
  ],

Config with headless browser
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you think its too slow to open browser every time, its also support to run test with headless browser which would be bit quicker.

.. code-block:: bash
    
  npm i --save-dev karma-phantomjs-launcher


Karma.conf.js

.. code-block:: javascript
  
  plugins: [
    require('karma-jasmine'),
    require('karma-phantomjs-launcher'),
    require('karma-jasmine-html-reporter'),
    require('karma-coverage-istanbul-reporter'),
    require('@angular/cli/plugins/karma')
  
  //...other config
  browsers: ['PhantomJS', 'Chrome'],

If the project is using ES6, while Phantomjs only supporting ES5, so error may happen:

.. code-block::

  PhantomJS 2.1.1 (Mac OS X 0.0.0) ERROR
    SyntaxError: Use of reserved word 'class'
    at http://localhost:9877/_karma_webpack_/polyfills.js:3166:0

  PhantomJS 2.1.1 (Mac OS X 0.0.0) ERROR
    SyntaxError: Use of reserved word 'class'
    at http://localhost:9877/_karma_webpack_/polyfills.js:3166:0

To fix it, `refering post https://stackoverflow.com/questions/29736114/how-to-use-es6-with-phantomjs

**Finally, another solution is to use Jest instead.**


TestCoverage
^^^^^^^^^^^^^^^^^

* Script in package.json could update to  **"test": "ng test --no-watch --code-coverage"**
* To setup threshold for test coverage:

.. code-block:: javascript
  
  coverageIstanbulReporter: {
    reports: [ 'html', 'lcovonly' ],
    fixWebpackSourcePaths: true,
    thresholds: {
      statements: 80,
      lines: 80,
      branches: 80,
      functions: 80
    }
  }
  

Reference
-------------

* `<https://angular.cn/guide/testing#testing>`_



