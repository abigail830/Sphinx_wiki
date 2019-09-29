SpringBoot Security
==========================

Add dependency in pom.xml

.. code-block:: xml
  
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
  </dependency>

Config security profile via application.yml

.. code-block:: yml
  
  spring:
    security:
      user:
        name: admin
        password: admin

Said if we added swagger and try to access via http://localhost:8080/swagger-ui.html#, at the moment it would have login screen popup. We could login via admin/admin, then swagger page would be shown.

If we not config the password via config, Springboot would try to generate one and shown in console log, such as:

  Using default security password: c8be15de-4488-4490-9dc6-fab3f91435c6


The same could verify via Junit test:

.. code-block:: java
  
  package com.github.abigail830.jwtdemo.api;

  import org.junit.Test;
  import org.junit.runner.RunWith;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.boot.test.context.SpringBootTest;
  import org.springframework.boot.test.web.client.TestRestTemplate;
  import org.springframework.http.HttpMethod;
  import org.springframework.http.HttpStatus;
  import org.springframework.http.ResponseEntity;
  import org.springframework.test.context.junit4.SpringRunner;
  
  import static org.junit.Assert.*;
  
  @SpringBootTest
  @RunWith(SpringRunner.class)
  public class AuthControllerTest {

      TestRestTemplate testRestTemplate;

      @Test
      public void test_hello_should_goto_login_page_when_not_login() {
          testRestTemplate = new TestRestTemplate();
          final ResponseEntity<String> result = testRestTemplate
                .exchange("http://localhost:8080/hello", HttpMethod.GET, null, String.class);

        assertEquals(HttpStatus.OK, result.getStatusCode());
        assertFalse(result.getBody().contains("Hello World"));
        assertTrue(result.getBody().contains("Please sign in"));
      }

      @Test
      public void test_hello_should_get_hello_world_when_login() {
        testRestTemplate = new TestRestTemplate("admin", "admin");
        final ResponseEntity<String> result = testRestTemplate
                .exchange("http://localhost:8080/hello", HttpMethod.GET, null, String.class);

        assertEquals(HttpStatus.OK, result.getStatusCode());
        assertTrue(result.getBody().contains("Hello World"));
        assertFalse(result.getBody().contains("Please sign in"));
      }
  }



.. index:: Security, SpringBoot, Authentication
