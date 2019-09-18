Service Testing
===========================

Case1. UserService will invoke HttpClient directly

.. code-block:: typescript
  
  import { Injectable } from '@angular/core';
  import { HttpClient, HttpParams } from '@angular/common/http';
  import { UserCredentials } from '../models/user-credentials';
  import { User } from '../models/user';
  import { Observable } from 'rxjs';
  
  @Injectable({
    providedIn: 'root'
  })
  export class UserService {
  
    readonly loginUrl = 'http://conduit.productionready.io/api/users/login';

    constructor(private http: HttpClient) { }
  
    login(userCredentials: UserCredentials): Observable<User> {
      return this.http.post<User>(this.loginUrl, userCredentials);
    }
  }

**Test with mock HttpClient**

* Remember to import HttpClientTestingModule
* Make use of HttpTestingController to simulate dummy HttpClient, with flush to return dummy result.
* For async function, remember to have 'done: DoneFn'/done().

.. code-block:: typescript
  
  import { TestBed } from '@angular/core/testing';

  import { UserService } from './user.service';
  import { UserCredentials } from '../models/user-credentials';
  import { HttpClientTestingModule, HttpTestingController } from '@angular/common/http/testing';
  import { User } from '../models/user';
  
  describe('UserService', () => {
   let service: UserService;
   let testingController: HttpTestingController;
  
   beforeEach(() => {
     TestBed.configureTestingModule({
       imports: [HttpClientTestingModule],
       providers: [UserService]
     });
     service = TestBed.get(UserService);
     testingController = TestBed.get(HttpTestingController);
  });
  
   afterEach(() => {
     testingController.verify();
   });
  
   it('Should able to get user when login success', (done: DoneFn) => {
    const userCredentials: UserCredentials = {
      email: 'email',
      password: 'password'
    };
    const expectUser: User = {
      email: 'email',
      username: 'username',
      token: 'token'
    };
    service.login(userCredentials).subscribe(user => {
      expect(user).toEqual(expectUser);
      done();
    }, fail);
  
    const request = testingController.expectOne(service.loginUrl);
    expect(request.request.method).toEqual('POST');
    request.flush(expectUser);
   });
  });
