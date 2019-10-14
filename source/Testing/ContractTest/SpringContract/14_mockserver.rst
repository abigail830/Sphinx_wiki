1.4 Mock Server
======================

利用契约生成挡板服务，主要为消费者的手动或自动化测试提供帮助

方式一
^^^^^^^

延续1.3 Step5里面提到的，plugin可以提供convert和generateStubs的步骤，创建出所需的stub.jar，这个jar包本来就可以直接java -jar的形式运行起来，这样手动测试的时候可以选择直接启动。

方式二
^^^^^^^^

可以在生产者编译时候，skip了以上两个步骤，另外单独建立一个mockserver项目，里面只运行这些步骤

.. code-block:: java
  
  package capbility.thoughtworks.com.stubrunner;

  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  import org.springframework.cloud.contract.wiremock.AutoConfigureWireMock;
  
  @SpringBootApplication
  @AutoConfigureWireMock
  public class StubRunnerApplication {
	  public static void main(String[] args) {
		  SpringApplication.run(StubRunnerApplication.class, args);
  	}
  }

.. code-block:: yaml
  
  wiremock:
    server:
      files:
      stubs:
      - file:build/stubs/**/mappings/**/*.json
      port: 6565




.. index:: Testing, Contract
