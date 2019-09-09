Vue With Axios
=========================

**Step 1**

* `Define the Request/Response object`_
* `To create the AXIOS http post`_
* `Config the API_URL in .env config`_



Define the Request/Response object
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: typescript
  
  export interface UserAuth {
    email: string;
    password: string;
    username: string;
  }
  
  export interface LoginResponse {
    user: User;
  }
  export interface User {
    email: string;
    token: string;
    username: string;
    bio?: string;
    image?: string;
  }

To create the AXIOS http post
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: typescript
  
  import axios from 'axios';
  import {User, UserAuth, LoginResponse } from '../models/user';

  const apiClient = axios.create({
    baseURL: process.env.API_URL,
    withCredentials: false, // This is the default
    headers: {
      'Accept': 'application/json',
      'Content-Type': 'application/json',
    },
    timeout: 10000,
  });

  export async function login(userAuth: UserAuth): Promise<User> {
    const response = await apiClient.post('/users/login', {
        user: userAuth,
    });
    return (response.data as LoginResponse).user;
  }

Config the API_URL in .env config
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* for vue-vli v3+, we only need to config in .env
* for older version, please refer to install dotenv first.

.. code-block::
  
  API_URL=https://conduit.productionready.io/api


Test for userApi.ts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**First, we could try test with real API**

.. code-block:: typescript
  
  import { login } from '@/services/userApi';

  describe('test for userApi', async () => {
    it('fail to login when pwd incorrect', async () => {
        // given
        const userAuth = {
            email: 'abigail830@163.com',
            password: '123456',
            username: 'Sara',
        };
        // when
        await login(userAuth)
            // then
            .catch((err) => {
                const actualResult = err.response.data.errors;
                const expectResult = {
                    'email or password': ['is invalid'],
                };
                expect(actualResult).toEqual(expectResult);
                expect(err.response.status).toBe(422);
        });
    });
  });

But of course, it is not recomment. Given once the backend server have issue, this test would be impacted to fail. More frequencely, we could mock the axios to ensure the unit test is independent and stable.







