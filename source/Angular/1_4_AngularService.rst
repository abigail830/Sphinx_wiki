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


