4.1. Vue with Jest
=====================

* `What are we testing?`_
* `Component Testing`_
* `Avoid`_
* `Reference`_


.. note::

  For the basic of Jest, please refer to another article `Jest Basic <http://wiki.saraqian.cn/Testing/Jest/Jest1.html>`_ . Here is focusing for testing with Vue. 参考测试策略，如果是单纯的ts/js逻辑部分，按照Jest Basic的测试直接写就可以了，这里关注的是Vue视图相关，如Component，View部分的UI界面及动作的测试。

What are we testing?
--------------------------

+------------------+----------------+----------------+
|    Input         |                |   Output       |
+==================+================+================+
| :props           |                | @events        |
+------------------+                +----------------+
| user interation  |                | render html    |
+------------------+     VUE        +----------------+
| child component  |   Component    | child component|
+------------------+                +----------------+
| VueX             |                | VueX           |
+------------------+                +----------------+
| Vue Route        |                | Vue Route      |
+------------------+                +----------------+
| Services         |                | Services       |
+------------------+----------------+----------------+

如：

* 当输入属性变更时候，界面html是否变更正确？如果该属性进一步传到子组件时，子组件是否正确？
* 当用户动作时，是否触发事件？属性是否跟着变更了？界面的变化是否正常？是否触发了子组件动作或界面变化？是否触发了vueX mutationAction？是否route发生跳转？是否触发其他服务类？
* 当子组件变化时，父组件是否正常？

通常配合Vue-test-utils进行（https://vue-test-utils.vuejs.org/zh/）


Component Testing
--------------------------------------------

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
  * mount/shallowMount - 渲染控件
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

* mount会完整渲染组件，shallowMount则是浅渲染，不会渲染里面的子组件。`参见具体区别 <http://wiki.saraqian.cn/Testing/Jest/mount.html>`_
* 想象，如果button是另外一个Button.vue, 显示部分又是另外一个Display.vue，那当我们在测试Count.vue的时候，依然可以使用上面的测试方法，把3个Vue组件都集成起来进行测试，可以叫Integration Test.
* 另一种想法，我们也可以单独的测试Count<->Display(当count输入prop时候能display出来, 和Count<->Button（当点击按钮的时候，count的prop会变化），那这种可以叫Shallow Test
* Basic test example could also refer to https://github.com/vuejs/vue-test-utils-jest-example


Refactor mount to factory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

假设有按钮组件，按钮上显示的字由props:msg决定,而权限由第二个参数props:propsData决定。

.. code-block:: javascript
  
  const msg = "submit"
  const factory = (propsData) => {
    return shallowMount(SubmitButton, {
      propsData: {
        msg,
        ...propsData
      }
    })
  }
  
  //const wrapper = factory()-->按钮显示submit，不是admin权限
  //const wrapper = factory({ isAdmin: true })-> 按钮显示submit，admin权限
  
  

Test with route
^^^^^^^^^^^^^^^^^^^^^^^

https://vue-test-utils.vuejs.org/guides/using-with-vue-router.html

.. code-block:: javascript
  
  import VueRouter from 'vue-router';
  
  const localVue = createLocalVue()
  localVue.use(VueRouter)
  const router = new VueRouter()
  
  describe('',()=>{
    // ...
    const wrapper = shallowMount(Login, {
              localVue,
              router,
        });
      // ...
  }


Avoid
----------

Believe Vue, not to test framework itself

.. code-block::

  <p>{{data}}</p>
  ...
  expect(p.text()).to.be('some prop value here')


.. seealso::
  
  Very good video: https://www.youtube.com/watch?v=OIpfWTThrK8


Reference
----------------

* https://lmiller1990.github.io/vue-testing-handbook/


.. index:: Testing, Jest, Vue
