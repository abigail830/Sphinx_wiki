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







.. index:: Microservices, Springboot, Eureka
