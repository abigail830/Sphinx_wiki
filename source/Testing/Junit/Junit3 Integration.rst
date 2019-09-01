组件测试
==========================

什么是组件测试？
------------------

.. image:: ../../../images/component_test.png
  :width: 500px

* 不但只针对某一个类/方法的，可能联通了几个类/方法一起进行测试
* 可能涉及连接数据库/三方服务的逻辑
* 如在spring中，从controller都service到dao到数据库进行通测。


思考一： 应该使用什么Database进行组件测试？
---------------------------------------------

使用跟生产环境一样的真实而又独立的数据库
""""""""""""""""""""""""""""""""""""""
  
- **好处**：与生产一致，从数据库配置，到SQL及其操作后的反馈都能完全模拟真实场景
- **弊端**：如果使用单一数据源，如使用开发环境的数据库，当测试并发运行时候就非常容易发生数据冲突的问题。尤其如果是跨团队共用同一个数据库，这种冲突会进一步加剧
- **建议解决思路**：
    
  * 使用腐败开发的思路，使用独立数据源。如本地运行时，在本地安装local版本的数据库，每人测试时只指向本地。在流水线上也使用一个独立于开发环境的数据库。
  * 再进一步， 在编写测试案例的时候，每个测试案例使用独立的id从测试数据上保证独立性。
  * 如果资源实在没有，遗留测试代码中也未必能轻易保证ID独立性的话，考虑使用H2等内存数据库运行测试（具体看下文）。
  
使用内存数据库
""""""""""""""""""""""""""""""""""""""
  
- **好处**：超级低成本的完成测试中数据隔离，避免数据冲突
- **弊端**：只能支持最通用的SQL，如果使用了某数据库特殊语法的场景下，H2有机会并不支持。另外，等于测试过程是与生产并不完全一致。如当期望测试数据库异常的场景下，H2返回的异常与其他数据库并不一样。

如何配置H2:
^^^^^^^^^^^^^^^^

pom.xml中添加依赖

.. code-block:: xml
  
  <dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>test</scope>
  </dependency>

在test/resource/application.properties或yaml中，配置

.. code-block:: yaml
  
  spring.datasource.url: jdbc:h2:mem:IntegrationTest;DB_CLOSE_DELAY=-1;MODE=MYSQL

* 其中的MODE配置可以根据生产实际使用的数据库类型配置。


如何在H2中创建和生产一样的表结构
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

这里可以借助一些业界的数据库版本管理工具进行，如flyway.

pom.xml

.. code-block:: xml
  
  <dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-core</artifactId>
    <scope>test<scppe>
  </dependency>

在resources/db/migration路径下，把各个版本的SQL按顺序放好。如V1__create_user_tbl.sql, V2__create_wish_table.sql, V3__update_age_column.sql...etc.

* 因为使用了H2和flyway的scope设置为test, 通过以上配置，flyway可以在spring运行起来的时候，自动按顺序运行migration下的所有sql。
* 如果在非测试环境下使用，如在生产环境对真实数据库配置flyway，它会自动记录下migration内哪些SQL是已经运行，然后只继续按顺序运行其他未运行过的SQL。

到这一步，内存版的数据库和表结构就建立好了，可以继续开始组件测试的编写。
  
 **谨记： 无论使用什么数据库，测试案例运行前后应做好数据清理**

