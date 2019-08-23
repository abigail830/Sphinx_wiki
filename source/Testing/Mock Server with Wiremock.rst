Mock Server with Wiremock
=================================

准备工作
^^^^^^^^^^^^

* 下载standalone jar： http://wiremock.org/docs/running-standalone/
* 启动wiremock： java -jar wiremock-standalone-2.24.1.jar

CRUD接口
^^^^^^^^^^^^
* 获取wiremock中所有接口：**GET** http://localhost:8080/__admin/mappings
* 添加接口： **POST** http://localhost:8080/__admin/mappings
* 把接口保存到standalone jar中： **POST** http://localhost:8080/__admin/mappings/save
* 删除某接口： **DELETE** http://localhost:8080/__admin/mappings?stubMappingId=cf64b47f-a0c0-4896-bfb9-8be0af31678d

.. index: Testing, Wiremock
