2. Test on Component 
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
    
    it(`should have as title 'demo'`, () => {
      const fixture = TestBed.createComponent(AppComponent);
      const app = fixture.debugElement.componentInstance;
      expect(app.title).toEqual('demo');
    });

    it('should render title in a h1 tag', () => {
      const fixture = TestBed.createComponent(AppComponent);
      fixture.detectChanges();
      const compiled = fixture.debugElement.nativeElement;
      expect(compiled.querySelector('h1').textContent).toContain('Welcome to demo!');
    });
  });

It is the test target for html contain below tag

.. code-block:: html
  
  <div style="text-align:center">
  <h1>
    Welcome to {{ title }}!
  </h1>

And ts contain below property

.. code-block:: typescript
  
  import { Component } from '@angular/core';

  @Component({
    selector: 'app-root',
    templateUrl: './app.component.html',
    styleUrls: ['./app.component.scss']
  })
  export class AppComponent {
    title = 'demo';
  }







