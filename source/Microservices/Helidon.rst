Oracle Helidon Framework
===========================


Helidon is a light weight web framework from Oracle. It included SE version and MP version.

MP Version
----------------

It is on top of SE version and provided more build in function, just like Springboot base on Spring. Said it could provide health endpoint and logging config by default. And we could easily add the openAPI support as well.

We could init the project with below command:

.. code-block:: bash
  
  mvn archetype:generate -DinteractiveMode=false \
    -DarchetypeGroupId=io.helidon.archetypes \
    -DarchetypeArtifactId=helidon-quickstart-mp \
    -DarchetypeVersion=0.10.5 \
    -DgroupId=com.github.abigail830 \
    -DartifactId=helidon-mp-demo \
    -Dpackage=com.github.abigail830.delidon.mp.demo


Startup log:
  
.. code-block:: 
  
  /Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/bin/java "-javaagent:/Applications/IntelliJ IDEA CE.app/Contents/lib/idea_rt.jar=58407:/Applications/IntelliJ IDEA CE.app/Contents/bin" -Dfile.encoding=UTF-8 -classpath /Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/charsets.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/deploy.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/cldrdata.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/dnsns.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/jaccess.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/jfxrt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/localedata.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/nashorn.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/sunec.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/sunjce_provider.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/sunpkcs11.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/zipfs.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/javaws.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/jce.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/jfr.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/jfxswt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/jsse.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/management-agent.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/plugin.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/resources.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/rt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/lib/ant-javafx.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/lib/dt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/lib/javafx-mx.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/lib/jconsole.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/lib/packager.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/lib/sa-jdi.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/lib/tools.jar:/Users/SaraQian/Documents/Github/helidon-mp-demo/target/classes:/Users/SaraQian/.m2/repository/io/helidon/microprofile/bundles/helidon-microprofile-1.2/0.10.5/helidon-microprofile-1.2-0.10.5.jar:/Users/SaraQian/.m2/repository/io/helidon/microprofile/bundles/helidon-microprofile-1.1/0.10.5/helidon-microprofile-1.1-0.10.5.jar:/Users/SaraQian/.m2/repository/io/helidon/microprofile/server/helidon-microprofile-server/0.10.5/helidon-microprofile-server-0.10.5.jar:/Users/SaraQian/.m2/repository/org/jboss/weld/se/weld-se-core/3.0.3.Final/weld-se-core-3.0.3.Final.jar:/Users/SaraQian/.m2/repository/org/jboss/weld/environment/weld-environment-common/3.0.3.Final/weld-environment-common-3.0.3.Final.jar:/Users/SaraQian/.m2/repository/org/jboss/weld/weld-core-impl/3.0.3.Final/weld-core-impl-3.0.3.Final.jar:/Users/SaraQian/.m2/repository/org/jboss/weld/weld-api/3.0.SP2/weld-api-3.0.SP2.jar:/Users/SaraQian/.m2/repository/org/jboss/weld/weld-spi/3.0.SP2/weld-spi-3.0.SP2.jar:/Users/SaraQian/.m2/repository/org/jboss/spec/javax/el/jboss-el-api_3.0_spec/1.0.7.Final/jboss-el-api_3.0_spec-1.0.7.Final.jar:/Users/SaraQian/.m2/repository/org/jboss/spec/javax/interceptor/jboss-interceptors-api_1.2_spec/1.0.0.Final/jboss-interceptors-api_1.2_spec-1.0.0.Final.jar:/Users/SaraQian/.m2/repository/org/jboss/logging/jboss-logging/3.2.1.Final/jboss-logging-3.2.1.Final.jar:/Users/SaraQian/.m2/repository/org/jboss/weld/probe/weld-probe-core/3.0.3.Final/weld-probe-core-3.0.3.Final.jar:/Users/SaraQian/.m2/repository/org/jboss/classfilewriter/jboss-classfilewriter/1.2.1.Final/jboss-classfilewriter-1.2.1.Final.jar:/Users/SaraQian/.m2/repository/org/slf4j/slf4j-api/1.7.25/slf4j-api-1.7.25.jar:/Users/SaraQian/.m2/repository/org/slf4j/slf4j-jdk14/1.7.25/slf4j-jdk14-1.7.25.jar:/Users/SaraQian/.m2/repository/io/helidon/webserver/helidon-webserver-netty/0.10.5/helidon-webserver-netty-0.10.5.jar:/Users/SaraQian/.m2/repository/io/helidon/common/helidon-common-reactive/0.10.5/helidon-common-reactive-0.10.5.jar:/Users/SaraQian/.m2/repository/io/netty/netty-codec-http/4.1.22.Final/netty-codec-http-4.1.22.Final.jar:/Users/SaraQian/.m2/repository/io/netty/netty-codec/4.1.22.Final/netty-codec-4.1.22.Final.jar:/Users/SaraQian/.m2/repository/io/netty/netty-handler/4.1.22.Final/netty-handler-4.1.22.Final.jar:/Users/SaraQian/.m2/repository/io/netty/netty-buffer/4.1.22.Final/netty-buffer-4.1.22.Final.jar:/Users/SaraQian/.m2/repository/io/netty/netty-common/4.1.22.Final/netty-common-4.1.22.Final.jar:/Users/SaraQian/.m2/repository/io/netty/netty-transport/4.1.22.Final/netty-transport-4.1.22.Final.jar:/Users/SaraQian/.m2/repository/io/netty/netty-resolver/4.1.22.Final/netty-resolver-4.1.22.Final.jar:/Users/SaraQian/.m2/repository/io/netty/netty-codec-http2/4.1.22.Final/netty-codec-http2-4.1.22.Final.jar:/Users/SaraQian/.m2/repository/io/helidon/webserver/helidon-webserver-jersey/0.10.5/helidon-webserver-jersey-0.10.5.jar:/Users/SaraQian/.m2/repository/io/opentracing/opentracing-api/0.31.0/opentracing-api-0.31.0.jar:/Users/SaraQian/.m2/repository/org/glassfish/jersey/core/jersey-server/2.26/jersey-server-2.26.jar:/Users/SaraQian/.m2/repository/org/glassfish/jersey/core/jersey-client/2.26/jersey-client-2.26.jar:/Users/SaraQian/.m2/repository/javax/validation/validation-api/1.1.0.Final/validation-api-1.1.0.Final.jar:/Users/SaraQian/.m2/repository/io/projectreactor/reactor-core/3.1.5.RELEASE/reactor-core-3.1.5.RELEASE.jar:/Users/SaraQian/.m2/repository/org/reactivestreams/reactive-streams/1.0.2/reactive-streams-1.0.2.jar:/Users/SaraQian/.m2/repository/org/glassfish/jersey/ext/cdi/jersey-weld2-se/2.26/jersey-weld2-se-2.26.jar:/Users/SaraQian/.m2/repository/org/glassfish/jersey/ext/cdi/jersey-cdi1x/2.26/jersey-cdi1x-2.26.jar:/Users/SaraQian/.m2/repository/org/glassfish/jersey/inject/jersey-hk2/2.26/jersey-hk2-2.26.jar:/Users/SaraQian/.m2/repository/org/glassfish/hk2/hk2-locator/2.5.0-b42/hk2-locator-2.5.0-b42.jar:/Users/SaraQian/.m2/repository/org/glassfish/hk2/external/aopalliance-repackaged/2.5.0-b42/aopalliance-repackaged-2.5.0-b42.jar:/Users/SaraQian/.m2/repository/org/glassfish/hk2/hk2-api/2.5.0-b42/hk2-api-2.5.0-b42.jar:/Users/SaraQian/.m2/repository/org/glassfish/hk2/hk2-utils/2.5.0-b42/hk2-utils-2.5.0-b42.jar:/Users/SaraQian/.m2/repository/org/javassist/javassist/3.22.0-CR2/javassist-3.22.0-CR2.jar:/Users/SaraQian/.m2/repository/org/glassfish/jersey/media/jersey-media-json-processing/2.26/jersey-media-json-processing-2.26.jar:/Users/SaraQian/.m2/repository/org/glassfish/jsonp-jaxrs/1.1.1/jsonp-jaxrs-1.1.1.jar:/Users/SaraQian/.m2/repository/io/helidon/microprofile/config/helidon-microprofile-config/0.10.5/helidon-microprofile-config-0.10.5.jar:/Users/SaraQian/.m2/repository/io/helidon/config/helidon-config-bundle/0.10.5/helidon-config-bundle-0.10.5.jar:/Users/SaraQian/.m2/repository/io/helidon/config/helidon-config/0.10.5/helidon-config-0.10.5.jar:/Users/SaraQian/.m2/repository/io/helidon/config/helidon-config-hocon/0.10.5/helidon-config-hocon-0.10.5.jar:/Users/SaraQian/.m2/repository/com/typesafe/config/1.3.3/config-1.3.3.jar:/Users/SaraQian/.m2/repository/io/helidon/config/helidon-config-yaml/0.10.5/helidon-config-yaml-0.10.5.jar:/Users/SaraQian/.m2/repository/org/yaml/snakeyaml/1.19/snakeyaml-1.19.jar:/Users/SaraQian/.m2/repository/io/helidon/security/helidon-security-tools-config/0.10.5/helidon-security-tools-config-0.10.5.jar:/Users/SaraQian/.m2/repository/io/helidon/common/helidon-common-key-util/0.10.5/helidon-common-key-util-0.10.5.jar:/Users/SaraQian/.m2/repository/io/helidon/common/helidon-common-configurable/0.10.5/helidon-common-configurable-0.10.5.jar:/Users/SaraQian/.m2/repository/io/helidon/microprofile/config/helidon-microprofile-config-cdi/0.10.5/helidon-microprofile-config-cdi-0.10.5.jar:/Users/SaraQian/.m2/repository/io/helidon/microprofile/health/helidon-microprofile-health-core/0.10.5/helidon-microprofile-health-core-0.10.5.jar:/Users/SaraQian/.m2/repository/org/eclipse/microprofile/config/microprofile-config-api/1.2.1/microprofile-config-api-1.2.1.jar:/Users/SaraQian/.m2/repository/org/osgi/org.osgi.annotation.versioning/1.0.0/org.osgi.annotation.versioning-1.0.0.jar:/Users/SaraQian/.m2/repository/org/eclipse/microprofile/health/microprofile-health-api/1.0/microprofile-health-api-1.0.jar:/Users/SaraQian/.m2/repository/javax/inject/javax.inject/1/javax.inject-1.jar:/Users/SaraQian/.m2/repository/io/helidon/microprofile/health/helidon-microprofile-health-checks/0.10.5/helidon-microprofile-health-checks-0.10.5.jar:/Users/SaraQian/.m2/repository/io/helidon/microprofile/metrics/helidon-microprofile-metrics-cdi/0.10.5/helidon-microprofile-metrics-cdi-0.10.5.jar:/Users/SaraQian/.m2/repository/javax/enterprise/cdi-api/2.0/cdi-api-2.0.jar:/Users/SaraQian/.m2/repository/javax/el/javax.el-api/3.0.0/javax.el-api-3.0.0.jar:/Users/SaraQian/.m2/repository/javax/interceptor/javax.interceptor-api/1.2/javax.interceptor-api-1.2.jar:/Users/SaraQian/.m2/repository/io/helidon/microprofile/metrics/helidon-microprofile-metrics/0.10.5/helidon-microprofile-metrics-0.10.5.jar:/Users/SaraQian/.m2/repository/io/helidon/microprofile/metrics/helidon-metrics-se/0.10.5/helidon-metrics-se-0.10.5.jar:/Users/SaraQian/.m2/repository/org/eclipse/microprofile/metrics/microprofile-metrics-api/1.1/microprofile-metrics-api-1.1.jar:/Users/SaraQian/.m2/repository/io/helidon/common/helidon-common/0.10.5/helidon-common-0.10.5.jar:/Users/SaraQian/.m2/repository/io/helidon/webserver/helidon-webserver/0.10.5/helidon-webserver-0.10.5.jar:/Users/SaraQian/.m2/repository/io/helidon/common/helidon-common-http/0.10.5/helidon-common-http-0.10.5.jar:/Users/SaraQian/.m2/repository/io/opentracing/opentracing-util/0.31.0/opentracing-util-0.31.0.jar:/Users/SaraQian/.m2/repository/io/opentracing/opentracing-noop/0.31.0/opentracing-noop-0.31.0.jar:/Users/SaraQian/.m2/repository/io/helidon/webserver/helidon-webserver-json/0.10.5/helidon-webserver-json-0.10.5.jar:/Users/SaraQian/.m2/repository/org/glassfish/jersey/media/jersey-media-json-binding/2.26/jersey-media-json-binding-2.26.jar:/Users/SaraQian/.m2/repository/org/glassfish/jersey/core/jersey-common/2.26/jersey-common-2.26.jar:/Users/SaraQian/.m2/repository/javax/ws/rs/javax.ws.rs-api/2.1/javax.ws.rs-api-2.1.jar:/Users/SaraQian/.m2/repository/javax/annotation/javax.annotation-api/1.3.1/javax.annotation-api-1.3.1.jar:/Users/SaraQian/.m2/repository/org/glassfish/hk2/external/javax.inject/2.5.0-b42/javax.inject-2.5.0-b42.jar:/Users/SaraQian/.m2/repository/org/glassfish/hk2/osgi-resource-locator/1.0.1/osgi-resource-locator-1.0.1.jar:/Users/SaraQian/.m2/repository/org/glassfish/javax.json/1.1.2/javax.json-1.1.2.jar:/Users/SaraQian/.m2/repository/javax/json/javax.json-api/1.1.2/javax.json-api-1.1.2.jar:/Users/SaraQian/.m2/repository/org/eclipse/yasson/1.0/yasson-1.0.jar:/Users/SaraQian/.m2/repository/javax/json/bind/javax.json.bind-api/1.0/javax.json.bind-api-1.0.jar:/Users/SaraQian/.m2/repository/org/jboss/jandex/2.0.4.Final/jandex-2.0.4.Final.jar:/Users/SaraQian/.m2/repository/javax/activation/javax.activation-api/1.2.0/javax.activation-api-1.2.0.jar com.github.abigail830.delidon.mp.demo.Main
  2019.12.05 12:25:18 INFO org.jboss.weld.Version Thread[main,5,main]: WELD-000900: 3.0.3 (Final)
  2019.12.05 12:25:18 INFO org.jboss.weld.Bootstrap Thread[main,5,main]: WELD-ENV-000020: Using jandex for bean discovery
  2019.12.05 12:25:19 INFO org.jboss.weld.Bootstrap Thread[main,5,main]: WELD-000101: Transactional services not available. Injection of @Inject UserTransaction not available. Transactional observers will be invoked synchronously.
  2019.12.05 12:25:19 INFO org.jboss.weld.Event Thread[main,5,main]: WELD-000411: Observer method [BackedAnnotatedMethod] private org.glassfish.jersey.ext.cdi1x.internal.CdiComponentProvider.processAnnotatedType(@Observes ProcessAnnotatedType) receives events for all annotated types. Consider restricting events using @WithAnnotations or a generic type with bounds.
  2019.12.05 12:25:20 WARN org.jboss.weld.Bootstrap Thread[main,5,main]: WELD-000146: BeforeBeanDiscovery.addAnnotatedType(AnnotatedType<?>) used for class org.glassfish.jersey.ext.cdi1x.internal.CdiComponentProvider$JaxRsParamProducer is deprecated from CDI 1.1!
  2019.12.05 12:25:20 INFO org.jboss.weld.Bootstrap Thread[main,5,main]: WELD-ENV-002003: Weld SE container b1d55030-83b3-43d1-8532-35ea0c14c8e6 initialized
  2019.12.05 12:25:21 信息 io.netty.util.internal.PlatformDependent Thread[main,5,main]: Your platform does not provide complete low-level API for accessing direct buffers reliably. Unless explicitly requested, heap buffer will always be preferred to avoid potential system instability.
  2019.12.05 12:25:21 信息 io.helidon.webserver.netty.NettyWebServer Thread[nioEventLoopGroup-2-1,10,main]: Channel '@default' started: [id: 0xce67c36e, L:/0:0:0:0:0:0:0:0:8080]
  2019.12.05 12:25:21 信息 io.helidon.microprofile.server.ServerImpl Thread[nioEventLoopGroup-2-1,10,main]: Server started on http://localhost:8080 (and all other host addresses) in 320 milliseconds.
  http://localhost:8080/greet

When we access http://localhost:8080/greet, then it would return "{"message":"Hello World!"}"


Health Check
```````````````````

When we access http://localhost:8080/health by default, it would already provided:

{"outcome":"UP","checks":[{"name":"deadlock","state":"UP"},{"name":"diskSpace","state":"UP","data":{"free":"45.23 GB","freeBytes":48562728960,"percentFree":"19.37%","total":"233.47 GB","totalBytes":250685575168}},{"name":"heapMemory","state":"UP","data":{"free":"121.59 MB","freeBytes":127499008,"max":"1.78 GB","maxBytes":1908932608,"percentFree":"98.00%","total":"158.00 MB","totalBytes":165675008}}]}

We could also customize it by following https://helidon.io/docs/latest/#/guides/07_health_mp_guide


Metrics
````````````

Default metrics:

- /metrics/base - Base metrics data as specified by the MicroProfile Metrics specification.
- /metrics/vendor - Helidon-specific metrics data.
- /metrics/application - Application-specific metrics data.

Good usage is it could help to count the times entering diff class/method/fields. Such as:

.. code-block:: java
  
    @GET
    @Produces(MediaType.APPLICATION_JSON)
    @Counted(name = "anyCard", monotonic = true)
    public JsonObject anyCard() throws InterruptedException {
        return createResponse("Here are some random cards ...");
    }

Then in application metrics it would have :

{
    "com.github.abigail830.helidon.mp.demo.api.GreetingCards.anyCard": 1,
    "com.github.abigail830.helidon.mp.demo.api.GreetResource.helloName": 0,
    "com.github.abigail830.helidon.mp.demo.api.GreetResource.helloWorld": 0
}

Detail refer to https://helidon.io/docs/latest/#/guides/09_metrics_mp_guide


OpenAPI
```````````

Adding plugin and dependency. The version is bit strange, seems very easy to conflict and make the server cannot up. Now I am using all be 1.1.2 version

.. code-block:: xml
  
        <plugin>
                <groupId>org.jboss.jandex</groupId>
                <artifactId>jandex-maven-plugin</artifactId>
                <version>1.0.6</version>
                <executions>
                    <execution>
                        <id>make-index</id>
                        <goals>
                            <goal>jandex</goal>
                        </goals>
                    </execution>
                </executions>
        </plugin>
            
        <dependency>
            <groupId>org.eclipse.microprofile.openapi</groupId>
            <artifactId>microprofile-openapi-api</artifactId>
            <version>1.1.2</version>
        </dependency>

        <dependency>
            <groupId>io.helidon.microprofile.openapi</groupId>
            <artifactId>helidon-microprofile-openapi</artifactId>
            <version>1.1.2</version>
            <scope>runtime</scope>
        </dependency>

Then once access localhost:8080/openapi, it would automatically download the api

.. code-block:: yaml
  
  ---
  openapi: 3.0.1
  info:
    title: QuickStart API
    version: "1.1"
  paths:
    /cards:
      get:
        responses:
          200:
            description: OK
            content:
              application/json: {}
    /greet:
      get:
        responses:
          200:
            description: OK
            content:
              application/json: {}
    /greet/greeting/{greeting}:
      put:
        parameters:
        - name: greeting
          in: path
          required: true
          schema:
            type: string
        responses:
          200:
            description: OK
            content:
              application/json: {}
    /greet/{name}:
      get:
        parameters:
        - name: name
          in: path
          required: true
          schema:
            type: string
        responses:
          200:
            description: OK
            content:
              application/json: {}

It would not as Swagger to open as a beautiful web page.
