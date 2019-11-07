Eureka Client
=====================

Simple example
------------------

pom.xml
^^^^^^^^^^^

.. code-block:: xml
  
  <dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
  </dependency>


EurekaClientApplication.java 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

adding @EnableEurekaClient

.. code-block:: java
  
  @SpringBootApplication
  @EnableEurekaClient
  public class EurekaClientApplication {

  	public static void main(String[] args) {
	  	SpringApplication.run(EurekaLoadBalanceApplication.class, args);
  	}
  }

application.yml

.. code-block:: yaml
  
  spring:
    application:
      name: eureka-provider1
    cloud:
      config:
        enabled: true
    eureka:
      instance:
        hostname: provider1
      client:
        registerWithEureka: true
        fetchRegistry: true
        serviceUrl:
          defaultZone: http://peer1:8761/eureka/,http://peer2:8762/eureka/,http://peer3:8763/eureka/


注意
-------

当服务刚起来的时候，并不是马上就可用（可被EurekaServer调用），可以留意服务的日志

.. code-block::

	2019-11-07 23:05:18.704  INFO 32751 --- [           main] c.g.a.e.EurekaDiscoveryClientApplication : Started 	EurekaDiscoveryClientApplication in 12.606 seconds (JVM running for 14.169)
	2019-11-07 23:05:48.504  INFO 32751 --- [freshExecutor-0] com.netflix.discovery.DiscoveryClient    : Disable delta property : false
	2019-11-07 23:05:48.504  INFO 32751 --- [freshExecutor-0] com.netflix.discovery.DiscoveryClient    : Single vip registry refresh property : null
	2019-11-07 23:05:48.504  INFO 32751 --- [freshExecutor-0] com.netflix.discovery.DiscoveryClient    : Force full registry fetch : false
	2019-11-07 23:05:48.504  INFO 32751 --- [freshExecutor-0] com.netflix.discovery.DiscoveryClient    : Application is null : false
	2019-11-07 23:05:48.504  INFO 32751 --- [freshExecutor-0] com.netflix.discovery.DiscoveryClient    : Registered Applications size is zero : true
	2019-11-07 23:05:48.504  INFO 32751 --- [freshExecutor-0] com.netflix.discovery.DiscoveryClient    : Application version is -1: false
	2019-11-07 23:05:48.504  INFO 32751 --- [freshExecutor-0] com.netflix.discovery.DiscoveryClient    : Getting all instance registry info from the eureka server
	2019-11-07 23:05:48.559  INFO 32751 --- [freshExecutor-0] com.netflix.discovery.DiscoveryClient    : The response status is 200





.. index:: Microservices, Springboot, Eureka
