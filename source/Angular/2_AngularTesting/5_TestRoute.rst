5. Test with Router
===========================

Background
---------------

* Assume we have a main routing module as entry point, in main page it having nav between main/login/register
* Main page also have a button, once clicked, it would route to Register page.
* Having 2 sub component page Login & Register.

main.component.ts

.. code-block:: typescript
  
  import { Component, OnInit } from '@angular/core';
  import { Router } from '@angular/router';
  
  @Component({
    selector: 'app-main',
    templateUrl: './main.component.html',
    styleUrls: ['./main.component.scss']
  })
  export class MainComponent implements OnInit {
  
    constructor(private router: Router) { }
    ngOnInit() {
    }
    gotoRegister() {
      this.router.navigateByUrl('/register');
    }
  }

main.component.html

.. code-block:: html
  
  <nav>
    <a routerLink="/">Main</a>
    <a routerLink="/login">Login</a>
    <a routerLink="/register">Register</a>
  </nav>
  <button (click)="gotoRegister()">Go To Register</button>

main-routing.module.ts

.. code-block:: typescript
  
  import { NgModule } from '@angular/core';
  import { Routes, RouterModule } from '@angular/router';
  import { LoginComponent } from './login/login.component';
  import { RegisterComponent } from './register/register.component';
  import { MainComponent } from './main.component';

  const routes: Routes = [
    {
      path: 'login',
      component: LoginComponent
    },
    {
      path: 'register',
      component: RegisterComponent
    },
    {
      path: '',
      component: MainComponent
    }
  ];

  @NgModule({
    imports: [RouterModule.forChild(routes)],
    exports: [RouterModule]
  })
  export class MainRoutingModule { }


To test the button click
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Keys**

* const routerSpy = jasmine.createSpyObj('Router', ['navigateByUrl']);
* { provide: Router, useValue: routerSpy }
* expect(routerSpy.navigateByUrl).toHaveBeenCalledWith('/register');

.. code-block:: typescript
  
  import { async, ComponentFixture, TestBed, fakeAsync, tick } from '@angular/core/testing';
  
  import { MainComponent } from './main.component';
  import { Router } from '@angular/router';
  
  describe('MainComponent', () => {
    let component: MainComponent;
    let fixture: ComponentFixture<MainComponent>;
    const routerSpy = jasmine.createSpyObj('Router', ['navigateByUrl']);
  
    beforeEach(async(() => {
      TestBed.configureTestingModule({
        declarations: [MainComponent],
        providers: [
          { provide: Router, useValue: routerSpy }
        ]
      }).compileComponents();
    }));
  
    beforeEach(() => {
      fixture = TestBed.createComponent(MainComponent);
      component = fixture.componentInstance;
      fixture.detectChanges();
    });
  
    it('should create', () => {
      expect(component).toBeTruthy();
    });
  
    it('Test button go to register', async(() => {
      const button = fixture.debugElement.nativeElement.querySelector('button');
      button.click();
      expect(routerSpy.navigateByUrl).toHaveBeenCalled();
      expect(routerSpy.navigateByUrl).toHaveBeenCalledWith('/register');
    }));
  
  });

同理，也可以自己制造一个Stub的class

.. code-block:: typescript
  
  class RouterStub {
    navigateByUrl(url: string) { return url; }
  }
  TestBed.configureTestingModule({
    declarations: [HomePageContentComponent],
    providers: [
      { provide: Auth, useClass: AuthStub },
      { provide: Router, useClass: RouterStub }
    ]
  }).compileComponents()



.. index:: Angular, Testing, Jasmine
