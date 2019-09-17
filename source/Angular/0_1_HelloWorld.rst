0.1. Hello World of Angular
============================

.. image:: ../../images/helloworld.png
  :width: 600px

基础ABC
---------------

app.component.html

.. code-block:: HTML

  <div id="title">Hello World</div>


app.component.scss

.. code-block:: CSS
  
  .title {
    text-align: center
  }

数据绑定
----------

app.component.html

.. code-block:: HTML

  <div id="title">{{content}}</div>


app.component.ts

.. code-block:: TYPESCRIPT
  
  import { Component } from '@angular/core';

  @Component({
    selector: 'app-root',
    templateUrl: './app.component.html',
    styleUrls: ['./app.component.scss']
  })
  export class AppComponent {
    title = ‘angular-demo';
    content = 'Hello World';
  }


Angular模版基础关键字
--------------------------------

1. **条件判断ng-if**

.. code-block:: HTML

  <div>{{content}} 
    <div *ngIf="name == ‘sara'">{{name}}</div>
  </div>

  
2. **条件else**

.. code-block:: HTML

  <div *ngIf="name; else welcome">{{content}} {{name}}</div>
  <ng-template #welcome>Welcome! Please logon</ng-template>


3. **For循环**

  * ngFor="let ppl of nameList"

.. code-block:: HTML

  <div>{{content}} 
    <div *ngFor="let ppl of nameList">{{ppl}}</div>
  </div>
 

.. code-block:: TYPESCRIPT

  nameList: string[];
  this.nameList = ['Kevin', 'Amy', 'Bonson'];



.. index:: angular, UI

