Hello World of Angular
============================

.. image:: ../../images/helloworld.png
  :width: 600px

Basic steps
---------------

app.component.html
^^^^^^^^^^^^^^^^^^^^^^
.. code-block:: HTML

  <div id="title">Hello World</div>


app.component.scss
^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: CSS
  
  .title {
    text-align: center
  }

Value assign via Typescript
-------------------------------

app.component.html
^^^^^^^^^^^^^^^^^^^^^^
.. code-block:: HTML

  <div id="title">{{content}}</div>

app.component.ts
^^^^^^^^^^^^^^^^^^^^^^
.. code-block:: javascript
  
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


More basic Angular template
--------------------------------

**条件判断ng-if**

.. code-block:: HTML

  <div>{{content}} 
    <div *ngIf="name == ‘sara'">{{name}}</div>
  </div>
  
 **条件else**

.. code-block:: HTML

  <div *ngIf="name; else welcome">{{content}} {{name}}</div>
  <ng-template #welcome>Welcome! Please logon</ng-template>

**For循环**

* ngFor="let ppl of nameList"

.. code-block:: HTML

  <div>{{content}} 
    <div *ngFor="let ppl of nameList">{{ppl}}</div>
  </div>
 
.. code-block:: TYPESCRIPT

  nameList: string[];
  this.nameList = ['Kevin', 'Amy', 'Bonson'];





