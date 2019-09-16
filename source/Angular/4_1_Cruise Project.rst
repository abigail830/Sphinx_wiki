4.1 Cruise
==============

Project Setup
--------------------

Step 1. Create project and add code style
.. code-block:: 

  ng new cruise
  yarn add prettier --dev
  
Step 2. Add proxy.conf.js(跨域问题）

.. code-block:: javascript
  
    const PROXY_CONFIG = {
    '/api': {
      target: 'http://localhost:8000',
      secure: false,
      changeOrigin: true,
      pathRewrite: { '^/api': '' }
    }
  };
  console.log('--------------------------------------------');
  console.log('[api] 8000');
  console.log('--------------------------------------------');
  module.exports = PROXY_CONFIG;

Step 3. Adding mock-server(optional)

* Basic json-server
* yarn install
* yarn start

Step 4. Modify package.json to refer proxy.conf

