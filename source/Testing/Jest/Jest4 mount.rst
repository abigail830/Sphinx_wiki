4. Snapshot Test - Mount和ShallowMount区别
=========================================================

用具体例子说明： 假设有组件Message, 里面包含子组件List,

List.vue

.. code-block:: javascript
  
  <template>
    <ul>
      <li :key="item" v-for="item in items">
        {{ item }}
      </li>
    </ul>
  </template>

  <script>
  export default {
    name: 'list',
    props: [
      'items'
    ]
  }
  </script>
  

Message.vue

.. code-block:: javascript
  
  <template>
    <div id="app">
      <List :items="items"></List>
    </div>
  </template>

  <script>
  import List from '../components/List.vue';

  export default {
      name: 'message',
      props: [
          'items'
      ],
      components: {
          List
      }
  }
  </script>


当我们测试Message的时候，可以有两种方式：
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* **方式一**： 完整的渲染父子组件进行集成测试


.. code-block:: javascript
  
  import Message from '@/components/Message.vue';
  import { mount, shallowMount } from '@vue/test-utils';

  describe('testing of Message.vue + List.vue', () => {
    it('Should able to display in List when receive props', () => {
        // given
        const items = ['1', '2', '3'];
        // when
        const wrapper = mount(Message, {
            propsData: { items },
        });
        // then
        expect(wrapper).toMatchSnapshot();
        expect(wrapper.findAll('li')).toHaveLength(items.length);
    });
  });

这时候渲染出来的snapshot是：

.. code-block::

  exports[`testing of Message.vue + List.vue Should able to display in List when receive props 1`] = `
  <div id="app">
    <ul>
      <li>
        1
      </li>
      <li>
        2
      </li>
      <li>
        3
      </li>
    </ul>
  </div>
  `;

* **方式二**： 浅渲染父组件进行单元测试

.. code-block:: javascript
  
  import Message from '@/components/Message.vue';
  import { mount, shallowMount } from '@vue/test-utils';
  
  describe('testing of Message.vue + List.vue', () => {
    it('Should able to display in Message when receive props', () => {
        // given
        const items = ['1', '2', '3', '4'];
        // when
        const wrapper = shallowMount(Message, {
            propsData: { items },
        });
        // then
        expect(wrapper).toMatchSnapshot();
        // expect(wrapper.contains('List')).toBe(true);
    });
  });

这时候渲染出来的snapshot是：

.. code-block::

  exports[`testing of Message.vue + List.vue Should able to display in Message when receive props 1`] = `
  <div id="app">
    <list-stub items="1,2,3,4"></list-stub>
  </div>
  `;

由此可以看出mount和shallowMount的区别 :-)

.. index:: Testing, Vue, Jest
