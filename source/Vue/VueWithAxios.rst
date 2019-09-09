Vue With Axios
=========================

**Raw Axios API**

* `Define the Request/Response object`_
* `To create the AXIOS http post`_
* `Config the API_URL in .env config`_
* `Test for userApi.ts`_

**Make use of API via Vue modules**

* `Make use of API in VueX modules`_

**Make use of API in Vue**

* `Make use of API via Vue`_

```````````````````````````````````````````````````````


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


Make use of API via Vue modules
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

store/modules/users.ts

.. code-block:: typescript
  
  import { Module, VuexModule, MutationAction, getModule } from 'vuex-module-decorators';
  import store from '../store';
  import { User, UserAuth } from '../../models/model';
  import { loginUser, setJWT } from '@/services/coinduitApi';

  @Module({
    namespaced: true,
    name: 'users',
    store,
    dynamic: true,
  })
  class UsersModule extends VuexModule {
    public user: User | null = null;
    get username() {
        return (this.user && this.user.username) || null;
    }
    @MutationAction
    public async login(loginReq: UserAuth) {
        const user = await loginUser(loginReq);
        setJWT(user.token);
        return { user };
    }
  }
  export default getModule(UsersModule);


Make use of API via Vue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Login.Vue

.. code-block:: javascript
  
  <template>
    <div class="auth-page">
  <div class="container page">
    <div class="row">

      <div class="col-md-6 offset-md-3 col-xs-12">
        <h1 class="text-xs-center">Login</h1>
        <p class="text-xs-center">
            <router-link to="/register">Need an account?</router-link>
        </p>

        <ul class="error-messages" v-if="loginError">
            <li>{{ loginError }}</li>
          </ul>

        <form>
            <fieldset class="form-group">
            <input class="form-control form-control-lg" type="text" v-model="username" placeholder="UserName">
          </fieldset>
          <fieldset class="form-group">
            <input class="form-control form-control-lg" type="text" v-model="email" placeholder="Email">
          </fieldset>
          <fieldset class="form-group">
            <input class="form-control form-control-lg" type="password" v-model="password" placeholder="Password">
          </fieldset>
          <button @click="login()" class="btn btn-lg btn-primary pull-xs-right">
            Sign in
          </button>
        </form>
      </div>
    </div>
  </div>
  </div>
  </template>
  
  <script lang="ts">
  import {Vue, Component} from 'vue-property-decorator';
  import users from '../store/modules/users';

  export default class Login extends Vue {
    private email = '';
    private password = '';
    private username = '';
    private loginError = '';

    public login() {
      users.login({
        email: this.email,
        password: this.password,
        username: this.username,
      })
      .then(() => {
        this.$router.push('/');
      })
      .catch((err) => {
        this.loginError = 'Invalid username or password';
      });
    }
  }
  </script>




