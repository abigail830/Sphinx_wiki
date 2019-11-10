SpringBoot Security(1)
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


**The same could verify via Junit test:**

.. code-block:: java
  
  //ignore those imports
  
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

但这时候测试，自动会弹出登陆，输入admin/admin之后可以获取比如swagger-ui.html，也可以执行http get的请求，但是http post的请求会得到403. 这是因为spring security中默认启动了CSRF保护机制

如果需要禁用CSRF，可以配置如下config：

* 新版Spring-Security强制要求密码加密，所以这里引入了BCryptPasswordEncoder（还需要在main设置@Bean创建才能autowired)
* configureGlobal部分负责authentication, 这里配置之后就不需要在application.yml设置user/password
* 下附如何让swagger的html可以不需要校验，需要配置路径比较多。
* 另外可以参考如何让如登陆页面无需授权，让admin对应api需要对应用户组，这些都是属于authorization授权
* 最后部分，如果httpBasic(),无权限会返回401. 如果使用formLogin()则会返回spring默认登陆页面（还可以进一步指定自定义登陆页面的）

.. code-block:: java
  
  @Configuration
  @EnableWebSecurity
  public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
  
    @Autowired
    BCryptPasswordEncoder bCryptPasswordEncoder;
  
    @Autowired
    public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
        auth
                .inMemoryAuthentication()
                .withUser("admin").password(bCryptPasswordEncoder.encode("admin")).roles("ADMIN")
                .and()
                .withUser("user").password(bCryptPasswordEncoder.encode("password")).roles("USER");
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.cors().
                and().csrf().disable().authorizeRequests()
                .antMatchers(HttpMethod.GET, "/swagger-ui.html","/swagger-resources/**",
                        "/webjars/**", "/configuration/security", "/configuration/ui",
                        "/v2/api-docs","/hello").permitAll()
                .antMatchers(HttpMethod.POST, SIGN_UP_URL, REGISTER_URL).permitAll()
                .antMatchers("/admins").hasRole("ADMIN")
                .and()
                .httpBasic();
  //            .anyRequest().authenticated()
  //            .and().formLogin();
    }
  }



Reference
---------------

* Spring Security: https://docs.spring.io/spring-security/site/docs/3.2.x/reference/htmlsingle/html5/
* CSRF： https://spring.io/blog/2013/08/21/spring-security-3-2-0-rc1-highlights-csrf-protection/
* https://blog.csdn.net/u013435893/article/details/79596628



.. index:: Security, SpringBoot, Authentication
