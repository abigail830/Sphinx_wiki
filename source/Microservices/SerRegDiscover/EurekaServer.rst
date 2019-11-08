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

下面只用了两个server组成cluster，留意里面的defaultZone在互指

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
        defaultZone: http://peer1:8761/eureka/
      registerWithEureka: false
      fetchRegistry: false


Heart Beat
----------------

作为服务注册与发现，因为连接本身都是基于间隔性的heart beat 心跳，另外一个非常重要的问题就是万一网络不好的时候怎么处理。

设想一下，服务A和服务B，自身都运作得很好，但因为偶然的网络问题，可能其中B与服务注册中心的心跳断了一段时间。理论上服务注册与发现的中心应当可以等待和自动重试一段时间，如果B resume的话，就可以继续正常工作。而如果重试到超时都未见到B的话，就可以考虑把B从列表中移除。

相关的属性参考：

**server端:**

* eureka.server.enable-self-preservation//（设为false，关闭自我保护主要）
* eureka.server.eviction-interval-timer-in-ms//清理间隔（单位毫秒，默认是60*1000）
  
**client端：**

* eureka.client.healthcheck.enabled = true//开启健康检查（需要spring-boot-starter-actuator依赖）
* eureka.instance.lease-renewal-interval-in-seconds =10//租期更新时间间隔（默认30秒）
* eureka.instance.lease-expiration-duration-in-seconds =30//租期到期时间（默认90秒）


HeartBeat问题，尤其是配合网络问题，是在做服务注册与发现时候必须面对的最重要问题。对于分布式系统，不能假设网络总是好的，当heartbeat处理不好，网络几秒的停顿可能导致很严重的问题。

同时，服务本身的stateless也是非常重要。

* 当服务可以彻底的无状态，服务之间就可以放心的使用HOT-HOT模式运行，即使网络停顿，可能也只是某个健在的服务忽然被移除而令另外一个服务压力大增。
* 但如果服务本身是stateful的，服务之间只能使用HOT-COLD的模式运行，比如PSD(Primary-Slave-Disaster)mode下，平时只有Primary是active的，如果探测到primary heartbeat不在了，就会启动Slave从standby mode到active。然而此时primary可能只是网络问题的假死，instance本身还是alive的。那当slave也启动好了，primary的网络也恢复了，就会出现split brain的问题，两个实例同时会处理同一个请求，很容易出现duplicate trade的严重问题。
 


.. index:: Microservices, Springboot, Eureka
