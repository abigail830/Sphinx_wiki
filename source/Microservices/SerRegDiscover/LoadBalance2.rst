Load Balance Client (2)
============================

有多种方式基于服务注册与发现机制去进一步实现负载均衡

Load Balance RestTemplate
-----------------------------

其中Client端跟例子1里面的一样，都提供了一个GET /name 接口。然后我们尝试使用加了Load Balance的Rest template方式进行访问两个client端

**EurekaLoadBalanceApplication.java**

.. code-block:: java
  
  @SpringBootApplication
  @EnableEurekaClient
  public class EurekaLoadBalanceApplication {
	  
  	@LoadBalanced
	  @Bean
  	public RestTemplate getRestTemplate(){
	  	return new RestTemplate();
  	}
  
	  public static void main(String[] args) {
		  SpringApplication.run(EurekaLoadBalanceApplication.class, args);
  	}
  }

**LoadBalanceController.java**

.. code-block:: java
  
  @RestController
  @Slf4j
  public class LoadBalanceController {

    @Autowired
    LoadBalanceService loadBalanceService;

    @GetMapping("/load-balance/{providerName}")
    public String loadBalanceProviderName(@PathVariable String providerName){
        return loadBalanceService.getProviderName(providerName);
    }
  }

**LoadBalanceService.java**

.. code-block:: java
  
  @Service
  @Slf4j
  public class LoadBalanceService {

    @Autowired
    private RestTemplate restTemplate;

    public String getProviderName(String providerName){
        log.info("This is via load balanced Method");
        return restTemplate.exchange("http://{providerName}/name",
                HttpMethod.GET,
                null,
                String.class,
                providerName).getBody();
    }
  }

细看这个处理方式，在Service部分其实跟调用普通的RestTemplate基本一样，唯一就是，这里是使用spring.application.name作为url去访问目的地。

而在测试的时候，会发现它是默认使用了轮询的方式1/2/1/2这样进行访问的。相对于DiscoveryClient的方式，就不需要额外的设计访问方式

.. index:: Springboot, Microservices, Load Balance

