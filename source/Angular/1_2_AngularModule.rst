1.2 Angular Module
==============================

* `Command to generate module`_
* `Keys in NgModule`_

  * `Common imports`_
  * `Enable trace for routing`_

* `Routing Setup`_
* `Active Route in UI`_


Command to generate module
-------------------------------

.. code-block:: bash
  
  ng generate module login
  ng generate module login --routing


2-3 File generated: login.module.ts/login.module.spec.ts/login-routing.module.ts

**login.module.ts**

.. code-block:: typescript
  
  import { NgModule } from '@angular/core';
  import { CommonModule } from '@angular/common';

  import { LoginRoutingModule } from './login-routing.module';
  import { LoginComponent } from './login.component';

  @NgModule({
    declarations: [LoginComponent],
    imports: [
      CommonModule,
      LoginRoutingModule
    ]
  })
  export class LoginModule { }

**login-routing.module.ts**

.. code-block:: typescript
  
  import { NgModule } from '@angular/core';
  import { Routes, RouterModule } from '@angular/router';

  const routes: Routes = [];
  
  @NgModule({
    imports: [RouterModule.forChild(routes)],
    exports: [RouterModule]
  })
  export class LoginRoutingModule { }

Keys in NgModule
----------------------

* **declarations:** 可声明对象，如属于本NgModule的组件、指令、管道
* **exports:** 导出，能在其他模块组件中使用的可生命对象子集 
* **imports:** 导入， 导出本模块组件时所需的其他模块的类
* **providers:** 本模块向全局服务中贡献的那些服务的创建器。这些服务能被本应用中任何部分使用（也可以在组件级别指定provider，这是首选方式）
* **bootstrap:** 应用主视图，根组件（如app.module.ts)。所有其他视图的宿主。

.. code-block:: typescript
  
  import { BrowserModule } from '@angular/platform-browser';
  import { NgModule } from '@angular/core';
  
  import { AppRoutingModule } from './app-routing.module';
  import { AppComponent } from './app.component';
  import { HeaderComponent } from './header/header.component';
  import { FooterComponent } from './footer/footer.component';
  import { HomeComponent } from './home/home.component';
  import { LoginComponent } from './login/login.component';
  
  @NgModule({
    declarations: [
      AppComponent,
      HeaderComponent,
      FooterComponent,
      HomeComponent,
      LoginComponent
    ],
    imports: [
      BrowserModule,
      AppRoutingModule,
    ],
    providers: [],
    bootstrap: [AppComponent]
  })
  export class AppModule { }

Common imports
^^^^^^^^^^^^^^^^^^^^

* **CommonModule:** when need to use NgIf and NgFor (it would come by default)
* **RouteModule:** when need routing function.Said RouterLink.forRoot() or .forChild()(it would come by default for routing module)
* **FormsModule:** When need to construct forms(it included NgModel)
* **ReactiveFormsModule:** for reactive forms
* **BrowserModule:** when need to run app in Browser
* **HttpClientModule:** when need to http task with backend server

Enable trace for routing
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Using '{ enableTracing: true }' in app.routing.module.ts

.. code-block:: typescript
  
  import { NgModule } from '@angular/core';
  import { RouterModule, Routes } from '@angular/router';
  
  const routes: Routes = [
    {
      path: 'dashboard',
      loadChildren: () => import('./dashboard/dashboard.module').then(mod => mod.DashboardModule),
      data: {
        title: 'Dashboard'
      }
    }
  ];

  @NgModule({
    imports: [RouterModule.forRoot(routes, { enableTracing: true })],
    exports: [RouterModule]
  })
  export class AppRoutingModule {}


Routing Setup
-----------------

* It would follow the config sequence as priority (So you may see the last config would be put at the bottom)
* The 1st sample below is using lazy load machanizm.

.. code-block:: typescript
  
  const routes: Routes = [
   {
     path: 'dashboard',
     loadChildren: () => import('./dashboard/dashboard.module').then(mod => mod.DashboardModule),
     data: {
       title: 'Dashboard'
     }
   },
   {
     path: 'agent/:id',
     component: AgentsModule
   },
   {
     path: '',
     redirectTo: '/dashboard',
     pathMatch: 'full'
   },
   {
     pathMatch: '**',
     component: PageNotFoundComponent
   }
 ];

Active Route in UI
-----------------------

Said after we defined routing "" & "login", in the header.component.html we could refer by this:

.. code-block:: html
 
 <ul class="nav navbar-nav pull-xs-right">
  <li class="nav-item">
   <a routerLink="/">Home</a>
  </li>
  <li class="nav-item">
   <a routerLink="/login">Login</a>
  </li>
 </ul>

And in app.component.html, we could have a general setup as:

.. code-block:: html
 
 <app-header></app-header>
 <router-outlet></router-outlet>
 <app-footer></app-footer>




.. index:: Angular
  
