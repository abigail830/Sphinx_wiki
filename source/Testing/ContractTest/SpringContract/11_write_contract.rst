1.1 Write Spring Contract 
============================

Spring Contract support 2 format: YAML & Groovy. Here focus on YAML format


* `Naming`_
* `Header`_
* `Request`_
* `Response`_

Naming
----------

* 如果没有另外配置，默认的契约放置路径在 src/test/resources/contracts,可以在里面再建立目录结构区分案例
* 每个yaml文件里面可以是一个案例，文件命名为should_do_sth_when_condition_fulfills


Header
----------

* **name:** should do sth when condition fulfils
* **description:** Some description
* **priority:** 8
* **ignored:** true

如果文件里面只有1个案例，文件名字也已经比较表意，那这里的name也可以不需要写了

.. code-block:: yaml
  
  description: This is the test for user controller
  name: should return http 200 when GET users
  priority: 8
  ignored: true


Request
-----------

* **url [mandatory]:** /foo
* **method [mandatory]:** POST|PUT|PATCH|GET|DELETE
* **headers :** key/value inside, such as Content-type: application/json
* **body :** could be like json object
* **queryParameters :** key/value inside, such as 'id: 1'
* **matchers :** for regular expression. Will further discussion in advance part

Example:

.. code-block:: yaml
  
  request:
    method: PUT
    url: /foo
    headers:
      foo: bar
      fooReq: baz
    queryParameters:
      a: b
      b: c
    body:
      foo: bar
    matchers:
      body:
        - path: $.foo
          type: by_regex
          value: bar
      headers:
        - key: foo
          regex: bar


Response
-----------

* **status :** http status such as 200/400/500..etc.
* **headers :** key/value inside, such as Content-type: application/json
* **body :** could be like json object
* **matchers :** for regular expression. Will further discussion in advance part

.. code-block:: yaml
  
  response:
    status: 200
    headers:
      foo2: bar
      foo3: foo33
      fooRes: baz
    body:
      foo2: bar
      foo3: baz
      nullValue: null
    matchers:
      body:
        - path: $.foo2
          type: by_regex
          value: bar
        - path: $.foo3
          type: by_command
          value: executeMe($it)
        - path: $.nullValue
          type: by_null
          value: null
      headers:
        - key: foo2
          regex: bar
        - key: foo3
          command: andMeToo($it)





.. index:: Testing, Contract