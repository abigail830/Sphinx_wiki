Fusing 熔断
====================

Provider
-----------

首先，假设有一个provider提供服务GET /name-delay, 为了模拟这个服务可能间歇性有性能问题，它里面暗地里会随机的sleep 10s

.. code-block:: java
  
  @RestController
  @Slf4j
  @RequestMapping("/name-delay")
  public class NameWithDelayController {

    @Value("${spring.eureka.instance.hostname}")
    String name;

    @GetMapping
    public String getName(){
        log.info("Receive naming request");
        randomDelay();
        return "Here is Eureka-Client With DiscoveryClient: " + name;
    }

    private void sleep(){
        try{
            Thread.sleep(10000);
        }catch (InterruptedException e){
            log.warn("Interrupted thread.sleep()");
        }
    }

    private void randomDelay(){
        Random random = new Random();
        final int rand = random.nextInt(3) + 1;
        if(rand==3)sleep();
    }
  }

Consumer
-------------

**pom.xml** 准备好hystrix依赖

.. code-block:: xml
  
  <dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
  </dependency>

**PerformanceController.java** 会触发200个GET /name-delay请求

.. code-block:: java
  
  @RestController
  @Slf4j
  @RequestMapping("/performance")
  public class PerformanceController {

    @Autowired
    DiscoveryService discoveryService;

    @Autowired
    LoadBalanceService loadBalanceService;
    
    @GetMapping("/circuit-break/{providerName}")
    public void loadBalanceProviderNameWithCB(@PathVariable String providerName){
        Instant start = Instant.now();
        int exceptionTimes = 0;
        for(int i=0;i<times;i++) {
            try{
                final String result = loadBalanceService.getProviderNameWithCircuitBreak(providerName);
                log.info("{} - {}", i,result);
            }catch (HystrixRuntimeException e){
                log.warn("{} - HystrixRuntimeException happened {} times", i,exceptionTimes++);
            }

        }
        Instant end = Instant.now();
        log.info("{}ms for {} times loop with load-balance",
                Duration.between(start,end).toMillis(),times);
    }
  }


Case1. Native
^^^^^^^^^^^^^^^^^^

**LoadBalanceService.java** 设想这里不做任何处理直接调用/name-delay,会发现当/name-delay慢的时候，该服务自己也会被拖累了。

.. code-block:: java
  
  @Service
  @Slf4j
  public class LoadBalanceService {
  
      @Autowired
      private RestTemplate restTemplate;
      
      public String getProviderNameWithCircuitBreak(String providerName){
        return restTemplate.exchange("http://{providerName}/name-delay",
                HttpMethod.GET,
                null,
                String.class,
                providerName).getBody();
    }
  }


Case2. Adding Timeout
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**LoadBalanceService.java** 为了不被拖累，最直观的做法就是增加一个timeout的配置，当provider过分慢的时候consumer就直接抛exception好了，那就只是部分的请求有exception，其他的还是可以正常服务

因为知道/name-delay会延时10s，所以这里把timeout设置到9s

.. code-block:: java
  
  @Service
  @Slf4j
  public class LoadBalanceService {
  
      @Autowired
      private RestTemplate restTemplate;
      
      @HystrixCommand(
            commandProperties = {
                    @HystrixProperty(name="execution.isolation.thread.timeoutInMilliseconds", value = "9000" )
            }
      )
      public String getProviderNameWithCircuitBreak(String providerName){
        return restTemplate.exchange("http://{providerName}/name-delay",
                HttpMethod.GET,
                null,
                String.class,
                providerName).getBody();
    }
  }

.. code-block:: 
  
  2019-11-08 09:28:09.552  INFO 33852 --- [nio-8081-exec-2] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:28:09.605  INFO 33852 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 0 - Here is Eureka-Client With DiscoveryClient: consumer1
  2019-11-08 09:28:09.616  INFO 33852 --- [nio-8081-exec-3] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:28:09.621  INFO 33852 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 1 - Here is Eureka-Client With DiscoveryClient: consumer1
  2019-11-08 09:28:09.626  INFO 33852 --- [nio-8081-exec-4] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:28:09.629  INFO 33852 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 2 - Here is Eureka-Client With DiscoveryClient: consumer1
  2019-11-08 09:28:09.633  INFO 33852 --- [nio-8081-exec-5] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:28:09.634  INFO 33852 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 3 - Here is Eureka-Client With DiscoveryClient: consumer1
  2019-11-08 09:28:09.639  INFO 33852 --- [nio-8081-exec-6] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:28:10.488  INFO 33852 --- [erListUpdater-0] c.netflix.config.ChainedDynamicProperty  : Flipping property: eureka-consumer1.ribbon.ActiveConnectionsLimit to use NEXT property: niws.loadbalancer.availabilityFilteringRule.activeConnectionsLimit = 2147483647
  2019-11-08 09:28:18.648  WARN 33852 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 4 - HystrixRuntimeException happened 0 times
  2019-11-08 09:28:18.653  INFO 33852 --- [nio-8081-exec-7] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:28:27.653  WARN 33852 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 5 - HystrixRuntimeException happened 1 times
  2019-11-08 09:28:27.660  INFO 33852 --- [nio-8081-exec-9] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:28:27.662  INFO 33852 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 6 - Here is Eureka-Client With DiscoveryClient: consumer1
  2019-11-08 09:28:27.668  INFO 33852 --- [io-8081-exec-10] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:28:27.670  INFO 33852 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 7 - Here is Eureka-Client With DiscoveryClient: consumer1
  2019-11-08 09:28:27.675  INFO 33852 --- [nio-8081-exec-2] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:28:27.676  INFO 33852 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 8 - Here is Eureka-Client With DiscoveryClient: consumer1
  2019-11-08 09:28:27.680  INFO 33852 --- [nio-8081-exec-3] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:28:27.685  INFO 33852 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 9 - Here is Eureka-Client With DiscoveryClient: consumer1
  2019-11-08 09:28:27.693  INFO 33852 --- [nio-8081-exec-4] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:28:36.688  WARN 33852 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 10 - HystrixRuntimeException happened 2 times
  2019-11-08 09:28:36.698  INFO 33852 --- [nio-8081-exec-6] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:28:45.694  WARN 33852 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 11 - HystrixRuntimeException happened 3 times
  2019-11-08 09:28:45.703  INFO 33852 --- [nio-8081-exec-9] c.g.a.e.api.NameWithDelayController      : Receive naming request

由此可见，部分抛出了exception，没有影响其他正常的访问，达到目的。


Case3. Degrade
^^^^^^^^^^^^^^^^^^

如果觉得抛出exception不够优雅，还可以进行降级处理 - 在provider异常的时候，返回某些默认值。

.. code-block:: java
  
  @Service
  @Slf4j
  public class LoadBalanceService {
  
      @Autowired
      private RestTemplate restTemplate;
      
      @HystrixCommand(
            commandProperties = {
                    @HystrixProperty(name="execution.isolation.thread.timeoutInMilliseconds", value = "9000" )
            },
            fallbackMethod = "getDefaultName"
      )
      public String getProviderNameWithCircuitBreak(String providerName){
        return restTemplate.exchange("http://{providerName}/name-delay",
                HttpMethod.GET,
                null,
                String.class,
                providerName).getBody();
      }
    
      public String getDefaultName(String providerName) {
          return "default name";
      }
  }

观察日志输出，这里当provider慢的时候， consumer就能够获取一个默认值

.. code-block:: java
  
  2019-11-08 09:48:52.782  INFO 34007 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 25 - Here is Eureka-Client With DiscoveryClient: consumer1
  2019-11-08 09:48:52.785  INFO 34007 --- [nio-8081-exec-8] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:48:52.786  INFO 34007 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 26 - Here is Eureka-Client With DiscoveryClient: consumer1
  2019-11-08 09:48:52.790  INFO 34007 --- [nio-8081-exec-9] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:48:54.788  INFO 34007 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 27 - default name
  2019-11-08 09:48:54.792  INFO 34007 --- [nio-8081-exec-3] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:48:54.793  INFO 34007 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 28 - Here is Eureka-Client With DiscoveryClient: consumer1
  2019-11-08 09:48:54.795  INFO 34007 --- [nio-8081-exec-4] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:48:54.797  INFO 34007 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 29 - Here is Eureka-Client With DiscoveryClient: consumer1
  2019-11-08 09:48:54.799  INFO 34007 --- [nio-8081-exec-7] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:48:56.799  INFO 34007 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 30 - default name
  2019-11-08 09:48:56.807  INFO 34007 --- [nio-8081-exec-8] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:48:58.805  INFO 34007 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 31 - default name
  2019-11-08 09:48:58.807  INFO 34007 --- [nio-8081-exec-3] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:48:58.809  INFO 34007 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 32 - Here is Eureka-Client With DiscoveryClient: consumer1
  2019-11-08 09:48:58.814  INFO 34007 --- [nio-8081-exec-4] c.g.a.e.api.NameWithDelayController      : Receive naming request
  2019-11-08 09:48:58.816  INFO 34007 --- [nio-8081-exec-1] c.g.a.e.api.PerformanceController        : 33 - Here is Eureka-Client With DiscoveryClient: consumer1




.. index:: Microservices, Springboot, Fuse, 
