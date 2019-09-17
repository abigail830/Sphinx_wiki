1.4 Angular Service
=========================

Command to generate service

.. code-block:: bash
  
  ng generate service util/api

Then it would create the util/api.service.ts and util/api.service.spec.ts

.. code-block:: typescript
  
  import { Injectable } from '@angular/core';
  
  @Injectable({
    providedIn: 'root'
  })
  export class ApiService {
  
    constructor() { }
  }

Injectable
--------------

Service could be inject to module and component via **providers**. Only only instance would be exist for the same inject level.

.. code-block:: typescript
  
  import { CommonModule } from '@angular/common';
  import { NgModule } from '@angular/core';
  import { AgentListComponent } from './agent-list/agent-list.component';
  import { AgentListService } from './agent-list/agent-list.service';
  import { AgentRoutingModule } from './agent-routing.module';
  import { AgentListStoreService } from './agent-list/agent-list-store.service';

  @NgModule({
    imports: [CommonModule, AgentRoutingModule],
    declarations: [AgentListComponent],
    providers: [AgentListService, AgentListStoreService]
  })
  export class AgentModule {}
  

Similar, we could also inject it into @Component via providers.




.. index:: Angular

