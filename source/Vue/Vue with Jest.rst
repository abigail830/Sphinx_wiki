Vue with Jest
=====================

* `Why Testing with Frontend`_
* `Testing framework`_
* `Vue Testing Guide`_
  
  *  `Start with vue component in javascript`_
  * `Avoid`_
  
  
* `Testing strategic`_

Why Testing with Frontend
------------------------------

* Confident / Remoral fear to change code
* Code Quality (Follow SRP / Better modular code / Understand what is my component doing)
* Documentation
* Developer happy wihtout 996


Testing framework
-----------------------

* **Jasmine**

  - BDD js测试框架, `官网 <https://jasmine.github.io/>`_

* **Jest**
  
  -  `官网 <https://jestjs.io/docs/en/dynamodb>`_ , 大包围，包括了测试框架和断言库和mock等所需元素，只需要引入一个包就可以。
  - 内置istanbul可以直接生成coverage报告

* **Mocha** 
  
  - js测试框架，不包含断言库，所以通常搭配chai使用，`官网 <https://mochajs.org/>`_
  - 由describe/it组成，也有before/after/beforeEach/afterEach， it.skip为跳过
  - mocha --recursive -R markdown > spec.md可以把测试用例生成文档
  
* **Karma**

  - 测试运行器，包含各种驱动,允许在浏览器下运行测试.
  - Angular默认就是Jasmine+Karma (但其实另外弹出浏览器会比较慢）, 也可以搭配Mocha使用
  
* **Chai**: 断言库

.. code-block:: javascript

  import chai from 'chai';
  let expect = chai.expect;
  
  expect(4 + 5).to.be.equal(9);
  expect(false).to.not.be.ok;
  expect(foo).to.be.an.instanceof(Foo);
  expect([1,2,3]).to.include(2);
  expect([]).to.be.empty;
 
* Sinon: 用于Mock

Vue Testing Guide
---------------------

For the basic of Jest, please refer to another article `Jest Basic <http://wiki.saraqian.cn/Testing/Jest.html#>`_

Here is focusing for testing with Vue.

Start with vue component in javascript
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

count.js

.. code-block:: javascript
  
  export default {
    template: `
      <div>
        <span class="count">{{ count }}</span>
        <button @click="increment">Increment</button>
      </div>
    `,

    data () {
      return {
        count: 0
      }
    },

    methods: {
      increment () {
        this.count++
      }
    }
  }

分析这个component, 输入可能有props，events，输出会有count，所以测试时候可以有不同的角度

* props->display: 渲染得到默认display是count=0
* event->props: 点击click得到props是1
* event->display: (综合集成考虑）默认时display是0， 点击click后，得到display是1


count.test.js
  * mount - 渲染控件
  * wrapper.vm - 获取vue component
  * wrapper.html() - 获取component的dom html
  * wrapper.vm.$data.count - 获取component内的data/props
  * wrapper.contains('button')/ wrapper.find('button') - 搜索控件

.. code-block:: javascript
  
  import { mount } from '@vue/test-utils'
  import Counter from './counter'

  describe('Counter', () => {
    // Now mount the component and you have the wrapper
    const wrapper = mount(Counter)

    it('renders the correct markup', () => {
      expect(wrapper.html()).toContain('<span class="count">0</span>')
    })

    // it's also easy to check for the existence of elements
    it('has a button', () => {
      expect(wrapper.contains('button')).toBe(true)
    })

    it('button should increment the count', () => {
      expect(wrapper.vm.count).toBe(0)
      const button = wrapper.find('button')
      button.trigger('click')
      expect(wrapper.vm.count).toBe(1)
    })
  })

**Remark**: 

* mount会完整渲染组件，shallowMount则是浅渲染，不会渲染里面的子组件。
* 想象，如果button是另外一个Button.vue, 显示部分又是另外一个Display.vue，那当我们在测试Count.vue的时候，依然可以使用上面的测试方法，把3个Vue组件都集成起来进行测试，可以叫Integration Test.
* 另一种想法，我们也可以单独的测试Count<->Display(当count输入prop时候能display出来, 和Count<->Button（当点击按钮的时候，count的prop会变化），那这种可以叫Shallow Test
* Basic test example could also refer to https://github.com/vuejs/vue-test-utils-jest-example


Test with route
^^^^^^^^^^^^^^^^^^^^^^^

**Imagine**: we have a Login page. It have field email/password and a 'SignIn' button, if inputted info and press button, suppose it would route to home page. So here we need to create a local dummy route

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
        .catch((err) => {
          console.error(err);
          this.loginError = 'Invalid username or password';
        });
    }
  }
  </script>

login.spec.ts

.. code-block:: typescript
  :linenos:
  :emphasize-lines: 1,3,5,6,7
  
  import { shallowMount,createLocalVue } from '@vue/test-utils';
  import Login from '../../src/views/Login.vue';
  import VueRouter from 'vue-router'
  
  const localVue = createLocalVue()
  localVue.use(VueRouter)
  const router = new VueRouter()

  describe('Login.vue', () => {
    it('should able to render login page', () => {
      // when
      const wrapper = shallowMount(Login,{
          localVue,
          router
        });
      // then
      expect(wrapper.contains('button')).toBe(true);
    });
  });




Avoid
^^^^^^^^^
Believe Vue, not to test framework itself

.. code-block:: 
  <p>{{data}}</p>
  ...
  expect(p.text()).to.be('some prop value here')


.. seealso::
  
  Very good video: https://www.youtube.com/watch?v=OIpfWTThrK8



Testing strategic
----------------------

We have another wiki talking about `vue with react <http://wiki.saraqian.cn/Testing/Jest%20with%20React.html>`_. There mentioned a triangle for my thought of testing strategic, we also mentioned some good/bad for that triangle. But actually there is also other thought on this point.

* Unit Test -> UI Test -> Snapshot Test
* Unit Test -> Snapshot Test -> E2E Test
* Unit Test -> Integration Test -> E2E Test

