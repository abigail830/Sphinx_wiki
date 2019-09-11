Funtional program in aliyun
===========================================

Before mainly for Java8 based setup.

Adding aliyun's lib in pom.xml
------------------------------------

.. code-block:: xml
  
  <dependency>
    <groupId>com.aliyun.fc.runtime</groupId>
    <artifactId>fc-java-core</artifactId>
    <version>1.0.0</version>
  </dependency>


First way is base on StreamRequestHandler
--------------------------------------------------

.. code-block:: java
  
  package com;

  import com.aliyun.fc.runtime.Context;
  import com.aliyun.fc.runtime.StreamRequestHandler;
  import java.io.IOException;
  import java.io.InputStream;
  import java.io.OutputStream;

  public class HelloWorld implements StreamRequestHandler {

    public void handleRequest(InputStream inputStream, OutputStream outputStream, Context context) throws IOException {
        context.getLogger().info("Hello World");
        outputStream.write(new String("hello world").getBytes());
    }
  }

Then in the aliyun's config page of functional programing, just input like **com.HelloWorld::handleRequest**, it would able to access and trigger this program.

Second way for using PojoRequestHandler<I, O>
------------------------------------------------------------------------

.. code-block:: java
  
  package com.echo;

  import com.aliyun.fc.runtime.Context;
  import com.aliyun.fc.runtime.PojoRequestHandler;
  
  public class Echo implements PojoRequestHandler<SimpleRequest, SimpleResponse> {

    public SimpleResponse handleRequest(SimpleRequest simpleRequest, Context context) {
        String message = "Hello " + simpleRequest.getRequestName();
        context.getLogger().info(message);
        return new SimpleResponse(message);
    }
  }

SimpleRequest and SimpleResponse is the common POJO object only having getter/setter.


Sometimes, we would also need to use 3rd party lib
----------------------------------------------------------

Here is an example for create a common encryt/decrypt function in serverless way.

In pom.xml, adding:

.. code-block:: xml
  
  <dependency>
            <groupId>com.aliyun.oss</groupId>
            <artifactId>aliyun-sdk-oss</artifactId>
            <version>2.6.1</version>
  </dependency>

  <dependency>
            <groupId>com.github.ulisesbocchio</groupId>
            <artifactId>jasypt-spring-boot-starter</artifactId>
            <version>2.1.0</version>
  </dependency>
        
  <build>
        <plugins>
            <plugin>
                <artifactId>maven-assembly-plugin</artifactId>
                <version>3.1.0</version>
                <configuration>
                    <descriptorRefs>
                        <descriptorRef>jar-with-dependencies</descriptorRef>
                    </descriptorRefs>
                    <appendAssemblyId>false</appendAssemblyId> <!-- this is used for not append id to the jar name -->
                </configuration>
                <executions>
                    <execution>
                        <id>make-assembly</id> <!-- this is used for inheritance merges -->
                        <phase>package</phase> <!-- bind to the packaging phase -->
                        <goals>
                            <goal>single</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
  </build>

Then, also create the necessary POJO for input/output. 

Last, implement the handleRequest for real proper.

.. code-block:: java
  
  package com.encrypt;

  import com.aliyun.fc.runtime.Context;
  import com.aliyun.fc.runtime.PojoRequestHandler;
  import org.jasypt.encryption.StringEncryptor;
  import org.jasypt.encryption.pbe.PooledPBEStringEncryptor;
  import org.jasypt.encryption.pbe.config.SimpleStringPBEConfig;

  public class DefaultDecryptor implements PojoRequestHandler<InputMessage, OutputMessage>, StringEncryptor {
    
    private StringEncryptor encryptor;
  
    public OutputMessage handleRequest(InputMessage inputMessage, Context context) {
        initEncryptor(inputMessage);
        final String result = decrypt(inputMessage.getMessage());
        return new OutputMessage(result);
    }
  
    private void initEncryptor(InputMessage inputMessage) {
        PooledPBEStringEncryptor encryptor = new PooledPBEStringEncryptor();
        SimpleStringPBEConfig config = new SimpleStringPBEConfig();
  
        if(inputMessage.getEnvKey()!=null){
            config.setPassword(inputMessage.getEnvKey());
        }else{
            config.setPassword("password");
        }
  
        config.setAlgorithm("PBEWithMD5AndDES");
        config.setKeyObtentionIterations("10000");
        config.setPoolSize("1");
        config.setProviderName("SunJCE");
        config.setSaltGeneratorClassName("org.jasypt.salt.RandomSaltGenerator");
        config.setStringOutputType("base64");
        encryptor.setConfig(config);
        this.encryptor = encryptor;
    }
  
    public String encrypt(String message) {
        return encryptor.encrypt(message);
    }
  
    public String decrypt(String encryptedMessage) {
        return encryptor.decrypt(encryptedMessage);
    }
  } 

.. index:: Serverless, Java

