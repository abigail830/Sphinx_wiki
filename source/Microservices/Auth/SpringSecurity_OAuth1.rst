Spring Security OAuth(1)
============================

3 steps to build a most simple OAuth Server
------------------------------------------------

Step1. Adding dependency

.. code-block:: xml
  
          <dependency>
            <groupId>org.springframework.security.oauth</groupId>
            <artifactId>spring-security-oauth2</artifactId>
            <version>2.2.1.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.security.oauth.boot</groupId>
            <artifactId>spring-security-oauth2-autoconfigure</artifactId>
            <version>2.2.1.RELEASE</version>
        </dependency>


Step2. Enable Auth Server

.. code-block:: java
  
  @SpringBootApplication
  @EnableAuthorizationServer
  public class OauthServerApplication {
	  public static void main(String[] args) {
		  SpringApplication.run(OauthServerApplication.class, args);
  	}
  }


Step3. Config client profile

.. code-block:: yml
  
  security:
    oauth2:
        client:
            client-id: client
            client-secret: 123456


Testing
-----------

**Raise Request**
curl client:123456@localhost:8080/oauth/token -dgrant_type=client_credentials -dscope=any

**Receive Response**
{"access_token":"120a1dd6-b5ca-4d42-b1bd-e0e66e2c0d96","token_type":"bearer","expires_in":43199,"scope":"any"}


.. index:: Microservices, Springboot,
