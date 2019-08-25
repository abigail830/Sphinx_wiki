RealWorldApp Example With Vue (1)
=========================================

`RealWorldApp <https://github.com/gothinkster/realworld>`_ is a very good example page could be used for practise different F/E and B/E language. Refering to the github, it already provided all necessary steps and basic file so that starter would be easier to follow.

`FrontEnd instruction click here <https://github.com/gothinkster/realworld-starter-kit/blob/master/FRONTEND_INSTRUCTIONS.md>`_


Build hardcode page with Vue
------------------------------------------

1. Init project with Cli
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* vue create real-world-app
* manually select dependencies at less included: yarn/router/vuex/jest/typescript
* install 2 additional dependencies

.. code-block:: bash
  
  yarn add vuex-module-decorators
  yarn add vue-property-decorator

2. Update Header
^^^^^^^^^^^^^^^^^^^^^^^^^

* create main.css(or scss) under src/assets/main.css, and copy the content from `this link <https://demo.realworld.io/main.css>`_
* In public/index.html, referring `Header <https://github.com/gothinkster/realworld-starter-kit/blob/master/FRONTEND_INSTRUCTIONS.md#header>`_ to copy 3 stylesheet dependencies
* update the index.html to refer to main.css create in step1.
* Remeber also to include this main.scss in main.ts - 'import '@/assets/main.scss';'

.. code-block:: html
  
    <head>
      <meta charset="utf-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width,initial-scale=1.0">
      <link rel="icon" href="<%= BASE_URL %>favicon.ico">
  
      <link rel="stylesheet" href="../src/assets/main.scss">
      <link href="//code.ionicframework.com/ionicons/2.0.1/css/ionicons.min.css" rel="stylesheet" type="text/css">
      <link href="//fonts.googleapis.com/css?family=Titillium+Web:700|Source+Serif+Pro:400,700|Merriweather+Sans:400,700|Source+Sans+Pro:400,300,600,700,300italic,400italic,600italic,700italic" rel="stylesheet" type="text/css">
      <title>RealWorldApp</title>
    </head>

3. Prepare Component & Views
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Create AppNavBar.vue under src/components and copy content from `here <https://github.com/gothinkster/realworld-starter-kit/blob/master/FRONTEND_INSTRUCTIONS.md#header>`_ , put <nav> part within <template>
* Create AppFooter.vue under src/components and copy content form `here <https://github.com/gothinkster/realworld-starter-kit/blob/master/FRONTEND_INSTRUCTIONS.md#footer>`_
* Create 8 pages according to `here <https://github.com/gothinkster/realworld-starter-kit/blob/master/FRONTEND_INSTRUCTIONS.md#pages>`_

4. Prepare Route
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: typescript
  
  export default new Router({
  mode: 'history',
  base: process.env.BASE_URL,
  routes: [
      {
        path: '/',
        name: 'home',
        component: Home,
      },
      {
        path: '/login',
        name: 'login',
        component: () => import(/* webpackChunkName: "about" */ './views/Login.vue'),
      },
      // others
      ],
  });

5. Update App.vue
^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block::
  
  <template>
    <div id="app">
      <AppNavBar></AppNavBar>
      <router-view/>
      <AppFooter></AppFooter>
    </div>
  </template>

  <script lang="ts">
  import {Vue, Component} from 'vue-property-decorator';
  import AppFooter from '@/components/AppFooter.vue';
  import AppNavBar from '@/components/AppNavBar.vue';
  
  @Component({
    components: {
      AppFooter, AppNavBar,
    },
  })
  export default class App extends Vue {
  }
  </script>

  <style lang="scss"></style>


Till here
``````````````

you would able to see the home page same as `demo <https://vue-vuex-realworld.netlify.com/#/>`_ , but the menu is not working. While you would still able to access those page via url, said http://localhost:8080/register

Update linkage with Route-Link
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* In Navbar.vue, update those <a> related place to <router-link to="/register">Need an account?</router-link>
* Similar, in Register.vue and Login.vue, also update the <a> to each other's route-link

After doing this
``````````````````````
Suppose all pages could link to each other and display correctly.


Remark
------------

* To use @ representing 'src/', it would need to ensure tsconfig.json included below info

.. code-block:: json
  
      "paths": {
      "@/*": [
        "src/*"
      ]
    },

* Knowledge point:

  * How to use component and view
  * How to import the css
  * How to setup the route with router.ts and route-link
  * How to import components in another Vue file

.. index:: Vue
