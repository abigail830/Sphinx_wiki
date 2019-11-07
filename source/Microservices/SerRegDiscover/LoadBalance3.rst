Load Balance Client (3)
==============================

有多种方式基于服务注册与发现机制去进一步实现负载均衡

Feign Client
-------------------

FeignClient其实是SpringCloud里面提供的，所以必须要引入SpringCloud（而不仅仅是Springboot)

**pom.xml**

.. code-block:: xml
  
  <dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
    <version>2.1.1.RELEASE</version>
  </dependency>


**EurekaFeignApplication.java**

.. code-block:: java
  
  @SpringBootApplication
  @EnableFeignClients
  public class EurekaFeignApplication {
  
	  public static void main(String[] args) {
		  SpringApplication.run(EurekaFeignApplication.class, args);
  	}
  }


**FeignController.java**

.. code-block:: java
  
  @RestController
  @Slf4j
  public class FeignController {

    @Autowired
    NameFeignClient nameFeignClient;

    @GetMapping("/feign/{providerName}")
    public String feignProviderName(@PathVariable String providerName){
       if(providerName.equals("eureka-consumer1"))
            return nameFeignClient.getName();
        else
            return "unknown provider";
    }
  }

**NameFeignClient.java**

.. code-block:: java
  
  @FeignClient("eureka-consumer1")
  public interface NameFeignClient {
    @RequestMapping(value = "/name", method = RequestMethod.GET)
    String getName();
  }

通常NameFeignClient会放在infrastructure层里面, 测试的时候会看到它默认同样是用轮询的方式1/2/1/2的进行。


Reference
-------------

对于如何在Feign里面添加拦截器或者熔断，可以参考：

* https://www.baeldung.com/spring-cloud-openfeign




.. index:: Microservices, Springboot, Load Balance
