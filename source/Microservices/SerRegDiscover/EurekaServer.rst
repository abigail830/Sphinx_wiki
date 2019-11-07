Eureka Server
===========================

When there are multiple service, we would need a centralize place could help to discover all services. In Springboot, this could be fulfill by Eureka Server

Simple example
--------------------

pom.xml
^^^^^^^^^^^^^^^^

.. code-block:: xml
  
  <dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
  </dependency>


EurekaServerApplication.java
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Just need to add @EnableEurekaServer

.. code-block:: java
  
  @SpringBootApplication
  @EnableEurekaServer
  public class EurekaServerApplication {
  
	  public static void main(String[] args) {
		  SpringApplication.run(EurekaServerApplication.class, args);
  	}
  }

Application.yml
^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: yaml

  spring:
    application:
      name: eureka-server
  client:
    registerWithEureka: false  # Server本身没必要再注册，Client才需要
    fetchRegistry: false  # Server本身没必要再获取，Client才需要
    
  eureka:
    instance:
      preferIpAddress: true  # Optional, 默认使用hostname
  server:
    waitTimeInMisWhenSyncEmpty: 5 # Optional, 心跳超时等待时间


Cluster
-----------

对于服务注册与发现，反正单点失败（single point of failure)至关重要，建立cluster是最直观的做法. 简单的话只需要配置比如三个application.yml(为了获取majority通常使用单数）

.. code-block:: yaml
  
  server:
    port: 8761
  eureka:
    instance:
      hostname: peer1
    client:
      serviceUrl:
        defaultZone: http://peer2:8762/eureka/
      registerWithEureka: false
      fetchRegistry: false


.. code-block:: yaml
  
  server:
    port: 8762
  eureka:
    instance:
      hostname: peer2
    client:
      serviceUrl:
        defaultZone: http://peer2:8761/eureka/
      registerWithEureka: false
      fetchRegistry: false


.. index:: Microservices, Springboot, Eureka
