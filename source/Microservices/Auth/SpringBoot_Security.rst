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


.. index:: Security, SpringBoot, Authentication
