2.1 Test with Jasmine & Karma
===========================================

By default, Angular contained **Jasmine & Karma**. 

And when generate component, by default it already come together with test case structure.

.. code-block:: javascript
  
  import { async, ComponentFixture, TestBed } from '@angular/core/testing';
  import { DashboardPageComponent } from './dashboard-page.component';
  import { RouterModule } from '@angular/router';
  
  describe('DashboardPageComponent', () => {
    let component: DashboardPageComponent;
    let fixture: ComponentFixture<DashboardPageComponent>;
  
    beforeEach(async(() => {
      TestBed.configureTestingModule({
        declarations: [ DashboardPageComponent ],
        imports: [ RouterModule.forRoot([]) ]
      })
      .compileComponents();
    }));
  
    beforeEach(() => {
      fixture = TestBed.createComponent(DashboardPageComponent);
      component = fixture.componentInstance;
      fixture.detectChanges();
    });
  
    it('should create', () => {
      expect(component).toBeTruthy();
    });
  });

When 'ng test', Karma by default would trigger the browser to open, run the test case in it and generate the report. Some points ppls use to complain Karma solution:

* It would recompile whole project every time, so it would be slower
* Testing report is not clear enough for some scenario
* Without snapshot test by compare with Jest

**Default Karma config for open browser:**

.. code-block:: javascript
  
  plugins: [
      require('karma-jasmine'),
      require('karma-chrome-launcher'),
      require('karma-jasmine-html-reporter'),
      require('karma-coverage-istanbul-reporter'),
      require('@angular-devkit/build-angular/plugins/karma')
  ],

Another way is using headless browser like phantomjs instead to make it bit faster

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

Script in package.json could also update to  **"test": "ng test --no-watch --code-coverage"**

If the project is using ES6, while Phantomjs only supporting ES5, so error may happen:

  PhantomJS 2.1.1 (Mac OS X 0.0.0) ERROR
    SyntaxError: Use of reserved word 'class'
    at http://localhost:9877/_karma_webpack_/polyfills.js:3166:0

  PhantomJS 2.1.1 (Mac OS X 0.0.0) ERROR
    SyntaxError: Use of reserved word 'class'
    at http://localhost:9877/_karma_webpack_/polyfills.js:3166:0

To fix it, `refering post <https://stackoverflow.com/questions/29736114/how-to-use-es6-with-phantomjs>`_

**Finally, another solution is to use Jest instead.**


Reference
-------------

* `<https://angular.cn/guide/testing#testing>`_



