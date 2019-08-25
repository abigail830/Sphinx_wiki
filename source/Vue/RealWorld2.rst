RealWorldApp 2
==========================

Continue update with real API
---------------------------------------

Suppose we could need to build a model to match the API' interface object. Some useful website: 

* https://jvilk.com/MakeTypes/
* http://json2ts.com/

Generate Model
^^^^^^^^^^^^^^^^^^^^^^^

* Create folder 'model' and file model.d.ts(it could also be separate files per interface)
* Copy the json from `here <https://github.com/gothinkster/realworld/tree/master/api>`_ and using above website convert to interface, and copy in above file.
* Or we could manually write the interface

.. code-block:: typescript
  
  export interface User {
    email: string;
    token: string;
    username: string;
    bio?: string;
    image?: string;
  }

  export interface Profile {
    username: string;
    bio?: string;
    image?: string;
    following: boolean;
  }

  export interface UserProfile {
    email: string;
    password: string;
  }


