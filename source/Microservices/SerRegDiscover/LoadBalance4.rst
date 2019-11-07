Performance of Load Balance
================================

之前，我们讨论了三种Load Balance的方式，这里尝试比较一下他们的性能：

* Discovery Client
* LoadBalance RestTemplate
* FeignClient
* Native(这里是直接访问不经过load balance)

比较方式：

.. code-block:: java
  
    @GetMapping("/discovery/{providerName}")
    public void discoveryProviderName(@PathVariable String providerName){
        Instant start = Instant.now();
        for(int i=0;i<times;i++){
            discoveryService.getProviderName(providerName);
        }
        Instant end = Instant.now();
        log.info("{}ms for {} times loop with discovery",
                Duration.between(start,end).toMillis(),times);
    }

    @GetMapping("/load-balance/{providerName}")
    public void loadBalanceProviderName(@PathVariable String providerName){
        Instant start = Instant.now();
        for(int i=0;i<times;i++) {
            loadBalanceService.getProviderName(providerName);
        }
        Instant end = Instant.now();
        log.info("{}ms for {} times loop with load-balance",
                Duration.between(start,end).toMillis(),times);
    }

    @GetMapping("/feign/{providerName}")
    public String feignProviderName(@PathVariable String providerName){
        Instant start = Instant.now();
        for(int i=0;i<times;i++) {
            nameFeignClient.getName();
        }
        Instant end = Instant.now();
        log.info("{}ms for {} times loop with native",
                Duration.between(start,end).toMillis(),times);
    }
    
    @GetMapping("/native/{providerName}")
    public void nativeProviderName(@PathVariable String providerName){
        Instant start = Instant.now();
        for(int i=0;i<times;i++) {
            nativeService.getProviderName(providerName);
        }
        Instant end = Instant.now();
        log.info("{}ms for {} times loop with native",
                Duration.between(start,end).toMillis(),times);
    }
    
当Times=200的时候, 貌似RestTemplate、Feign表现差不多，Native反而比较费时

* DiscoveryClient： 1057ms/965ms/684ms
* RestTemplate: 1198ms/671ms/592ms
* Feign: 1031ms/560ms/594ms
* Native: 1252ms/788ms/909ms




