Vue-CLI
==================

This is for VueCli > v3, if having vue version 2, could re-install via 

.. code-block:: bash
  
  npm uninstall -g vue-cli
  npm install -g @vue/cli
  
Steps
-----------

* Create project: 

.. code-block:: bash
  
  vue create cruise
  
  # Old command for v2: 
  # vue init webpack-simple cruise

* Start project with default pages:

.. code-block:: bash
  
  yarn run serve


**Remark**: 
* 在默认项目中并没有配置如jest等测试相关的依赖以及scripts
* 如果在create的时候选择是手动选择依赖的话，里面可以手动添加如unit/e2e test, typescript, vuex,router等的依赖,然后常用的依赖组合可以存为自定义的prefix，下次创建的时候直接使用
* 添加plugin的方式：vue add vuetify. 这里会添加@vue/cli-plugin-vuetify的插件（add的时候只需要打plugin后面的名字）

常用组件库
^^^^^^^^^^^^^^^^^

* ElementUI: `https://element.eleme.cn/#/zh-CN`_
* Vuetify: `https://vuetifyjs.com/zh-Hans/getting-started/quick-start`_
* Bootstrap-Vue: `https://bootstrap-vue.js.org/`_

.. index:: Vue

  
