1. Karma Basic
===========================================

Karma的配置集中在karma.config.js

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
------------------------------------

If you think its too slow to open browser every time, its also support to run test with headless browser which would be bit quicker.

**有两种配置方式：**

使用ChromeHeadless (最简单)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Karma.conf.js

.. code-block:: javascript
  
  //browsers: ['Chrome'],
  browsers: ['ChromeHeadless'],


使用phantomjs
^^^^^^^^^^^^^^^^^^^^

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
  browsers: ['PhantomJS'],

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

* 生成测试覆盖率报告命令："ng test --no-watch --code-coverage"
* 完成时，会在项目中创建一个新的 /coverage 目录。打开其 index.html 文件以查看代码覆盖率值的报告
* 如果希望每次都生成有两种方法：

  * 方法一： 更新"Script" in package.json  
    "test": "ng test --no-watch --code-coverage"**
  * 方法二： 配置angular.json
  
  .. code-block:: json
    
    "test": {
      "options": {
        "codeCoverage": true
      }
    }
    
  
  
  
  
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



