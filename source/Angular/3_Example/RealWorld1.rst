RealWorldApp Exmaple (1)
===========================================

Step1. Create project
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash
  
  ng new real-world
  
Step2. Add external css into index
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: html
  
  <link href="//code.ionicframework.com/ionicons/2.0.1/css/ionicons.min.css" rel="stylesheet" type="text/css">
  <link href="//fonts.googleapis.com/css?family=Titillium+Web:700|Source+Serif+Pro:400,700|Merriweather+Sans:400,700|Source+Sans+Pro:400,300,600,700,300italic,400italic,600italic,700italic" rel="stylesheet" type="text/css">
  <link rel="stylesheet" href="//demo.productionready.io/main.css">


Step3. Create component Header & Footer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash
  
  ng generate component header
  ng generate component footer

Copy the HTML content of header/footer into the newly created component's html

Reference: https://github.com/gothinkster/realworld-starter-kit/blob/master/FRONTEND_INSTRUCTIONS.md#header . But ensure not include those part like header/body/html, only include the inside part said <footer> and <nav>.

Step4. Create routing module and component of home & login page
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash
  
  ng generate component home
  ng generate module home --routing=true
  
  ng generate component login
  ng generate module login --routing=true

Step5. Config Routing of app
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**app-routing.modules.ts**

.. code-block:: typescript
  
  import { NgModule } from '@angular/core';
  import { Routes, RouterModule } from '@angular/router';
  import { HomeComponent } from './home/home.component';
  import { LoginComponent } from './login/login.component';

  const routes: Routes = [
    {
      path: '',
      component: HomeComponent
    },
    {
      path: 'login',
      component: LoginComponent
    }
  ];

  @NgModule({
    imports: [RouterModule.forRoot(routes)],
    exports: [RouterModule]
  })
  export class AppRoutingModule { }

**app.module.ts** ensure included all necessary module/components

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



Step6. Update app.component.html
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: html
  
  <app-header/>
  <router-outlet></router-outlet>
  <app-footer/>

So far, it should be working to show home page and login page by specify in url said http://localhost:4200/ and http://localhost:4200/login

Step7. Update header.html
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: html
  
     <nav class="navbar navbar-light">
      <div class="container">
        <a class="navbar-brand" href="index.html">conduit</a>
        <ul class="nav navbar-nav pull-xs-right">
          <li class="nav-item">
            <a routerLink="/">Home</a>
          </li>
          <li class="nav-item">
            <a routerLink="/login">Login</a>
          </li>
          
        </ul>
      </div>
    </nav>

Fianlly, till here, the top menu should be also working.

.. image:: ../../../images/home_angular.png
  :width: 550px


**Source code refer to https://github.com/abigail830/real-world-angular**


.. index:: Angular

