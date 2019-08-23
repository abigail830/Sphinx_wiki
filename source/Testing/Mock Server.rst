Mock Server
======================

Different ways to create mock server:

* `Json-Server`_


Json-Server
----------------------

Only 3 steps within 10mins to create a mock sever:

* Step1. npm install -g json-server
* Step2. create db.json with the response json

.. code-block:: json
  
  {
    "users": [
        {
            "id": 1,
            "name": "Cherry",
            "age": 30
        },
        {
            "id": 2,
            "name": "Saving",
            "age": 35
        }
    ]
}

* Step3. json-server db.json

.. code-block:: bash
  
  99-1-185-165:mock-server SaraQian$ json-server db.json

  \{^_^}/ hi!

  Loading db.json
  Done

  Resources
  http://localhost:3000/users

  Home
  http://localhost:3000

  Type s + enter at any time to create a snapshot of the database


.. image:: ../../images/json-server.png
  :width: 500px



