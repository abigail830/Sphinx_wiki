CSRF via Spring Security
==================================


CSRF默认已经在SpringSecurity中开启

.. code-block:: java
  
  @EnableWebSecurity
  @Configuration
  public class WebSecurityConfig extends
     WebSecurityConfigurerAdapter {
  
    @Override
    protected void configure(HttpSecurity http) throws Exception {
      http
        .csrf().disable()
        ...;
    }
  }

然后使用不同方式在前端提交时候加上CSRF Token。 例子参考： https://spring.io/blog/2013/08/21/spring-security-3-2-0-rc1-highlights-csrf-protection/
