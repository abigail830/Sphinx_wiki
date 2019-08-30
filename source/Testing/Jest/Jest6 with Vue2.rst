6.2. Vue test example for Login Page
=============================================

Login.vue

.. code-block:: typescript
  
  <template>
    <div class="auth-page">
      <div class="container page">
        <div class="row">
          <div class="col-md-6 offset-md-3 col-xs-12">
            <h1 class="text-xs-center">Sign in</h1>
            <p class="text-xs-center">
              <router-link to="/register"> Need an account? </router-link>
            </p>
  
            <ul class="error-messages" v-if="loginError">
              <li>{{ loginError }}</li>
            </ul>
  
            <form>
              <fieldset class="form-group">
                <input
                  class="form-control form-control-lg"
                  type="text"
                  v-model="email"
                  placeholder="Email"
                />
              </fieldset>
              <fieldset class="form-group">
                <input
                  class="form-control form-control-lg"
                  type="password"
                  v-model="password"
                  placeholder="Password"
                />
              </fieldset>
              <button
                @click="login()"
                class="btn btn-lg btn-primary pull-xs-right"
              >
                Sign in
              </button>
            </form>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script lang="ts">
  import { Vue, Component } from 'vue-property-decorator';
  import users from '@/store/modules/users';

  @Component
  export default class Login extends Vue {
    email = '';
    password = '';
    loginError = '';
  
    login() {
      users
        .login({
          email: this.email,
          password: this.password,
        })
        .then(() => this.$router.push('/'))
        .catch((err: Error) => {
          console.error(err);
          this.loginError = 'Invalid username or password';
        });
    }
  }
  </script>
  
当我打算测试这个Login.vue的时候，

* 如果单纯测视图，可以考虑用snapshot test,有或者是渲染了页面之后具体找控件assert一下看input和button是不是在。
* 如果测动作的话，可以考虑测试点击Sign in之后，如果后端返回正常，是不是会触发$router.push,如果后端不正常，是不是会给loginError赋值


视图测试
------------

.. code-block:: javascript
  
  import { shallowMount, createLocalVue, mount } from '@vue/test-utils';
  import Login from '../../src/views/Login.vue';
  import VueRouter from 'vue-router';
  
  const localVue = createLocalVue()
  localVue.use(VueRouter)
  const router = new VueRouter()
  
  describe('Login.vue', () => {
    // 手动找控件assert
    it('should able to render login page', () => {
        // when
        const wrapper = shallowMount(Login, {
            localVue,
            router,
        });
        // then
        expect(wrapper.contains('button')).toBe(true);

    });
    //snapshot test
    it('Snapshot test for verify Login Page render', () => {
        const wrapper = shallowMount(Login, {
            localVue,
            router
        });
        expect(wrapper).toMatchSnapshot();
    });
  });



动作测试
------------

.. code-block:: javascript
  
  import { shallowMount, createLocalVue, mount } from '@vue/test-utils';
  import Login from '../../src/views/Login.vue';
  import Home from '../../src/views/Home.vue';
  import VueRouter from 'vue-router';
  import { User } from '@/store/models';
  import users from '@/store/modules/users';
  
  const localVue = createLocalVue()

  describe('Login.vue', () => {
    it('should able to route to home page when press submit', async () => {
        //given
        expect.assertions(3);

        const $router = {
            push: jest.fn()
        };
        const router = new VueRouter({
            routes: [
                {
                    name: "home",
                    path: "/",
                    component: Home
                }
            ]
        });
        const dummyUser: User = {
            email: 'abigail830@163.com',
            username: 'Sara',
            token: 'This is a token'
        };
        users.login = jest.fn().mockResolvedValue(dummyUser);

        // when
        const wrapper = shallowMount(Login, {
            localVue,
            router,
            mocks: {
                $router
            }
        });
        expect(wrapper.contains('button')).toBe(true);
        await wrapper.find('button').trigger('click');

        // then
        expect(users.login).toHaveBeenCalled();
        expect($router.push).toHaveBeenCalled();
    });
  });








