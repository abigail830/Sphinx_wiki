2.1 Angular Testing with Jasmine & Karma
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

When 'ng test', Karma by default would trigger the browser to open, run the test case in it and generate the report. Some points ppls use to complant Karma solution:

* It would recompile whole project every time, so it would be slower
* Testing report is not clear enough for some scenario
* Without snapshot test by compare with Jest

Reference
-------------

* `<https://angular.cn/guide/testing#testing>`_



