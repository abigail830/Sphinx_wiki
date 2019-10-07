JWS、Token的认证例子（2）
=============================

这里也是使用Spring-Security的方式进行认证的例子。

* `代码摘要`_

  * `JWTAuthenticationFilter`_
  * `WebSecurityConfig`_
  * `UserDetailsServiceImpl`_

* `Advance`_
* `Reference`_



代码摘要
---------------

JWTAuthenticationFilter
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* 首先，使用Spring-Security之后，完全不需要担心RequestBody没办法读两次的问题，所以下面直接就req.getInputStream()获取body。
* uthenticationManager.authenticate进行认证工作。
* 这里继承了UsernamePasswordAuthenticationFilter，里面默认的是在POST+/login的时候进行认证（不冲载的话就不能使用其他URL）

.. code-block:: java
  
  public class JWTAuthenticationFilter extends UsernamePasswordAuthenticationFilter {
  
    @Autowired
    JWTAuth0Util jwtAuth0Util;
  
    private AuthenticationManager authenticationManager;
  
    public JWTAuthenticationFilter(AuthenticationManager authenticationManager) {
        this.authenticationManager = authenticationManager;
    }
  
    @Override
    public Authentication attemptAuthentication(HttpServletRequest req,
                                                HttpServletResponse res) throws AuthenticationException {
        try {
            ApplicationUser applicationUser = new ObjectMapper()
                    .readValue(req.getInputStream(), ApplicationUser.class);

            return authenticationManager.authenticate(
                    new UsernamePasswordAuthenticationToken(
                            applicationUser.getUsername(),
                            applicationUser.getPassword(),
                            new ArrayList<>())
            );
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
  
    @Override
    protected void successfulAuthentication(HttpServletRequest req,
                                            HttpServletResponse res,
                                            FilterChain chain,
                                            Authentication auth) throws IOException, ServletException {

        final String token = jwtAuth0Util.sign(((User) auth.getPrincipal()).getUsername());
        res.addHeader(HEADER_AUTHORIZATION, TOKEN_PREFIX + token);
    }
  }


WebSecurityConfig
^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: java
  
  @EnableWebSecurity
  public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
  
    private UserDetailsServiceImpl userDetailsService;
    private BCryptPasswordEncoder bCryptPasswordEncoder;
  
    public WebSecurityConfig(UserDetailsServiceImpl userDetailsService,
                             BCryptPasswordEncoder bCryptPasswordEncoder) {
        this.userDetailsService = userDetailsService;
        this.bCryptPasswordEncoder = bCryptPasswordEncoder;
    }
  
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.cors().and().csrf().disable()
                .authorizeRequests()
                .antMatchers(HttpMethod.POST, REGISTER_URL).permitAll()
                .anyRequest().authenticated()
                .and()
                .addFilter(new JWTAuthenticationFilter(authenticationManager()))
                .addFilter(new JWTAuthorizationFilter(authenticationManager()))
                // this disables session creation on Spring Security
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);
    }

    @Override
    public void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userDetailsService).passwordEncoder(bCryptPasswordEncoder);
    }
  }


UserDetailsServiceImpl
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

这里实现了UserDetailsService，为认证过程中如何获取用户信息做铺垫

.. code-block:: java
  
  @Service
  public class UserDetailsServiceImpl implements UserDetailsService {
  
    @Autowired
    ApplicationUserRepo applicationUserRepo;
  
    @Override
    public UserDetails loadUserByUsername(String userName) throws UsernameNotFoundException {
        final ApplicationUser user = applicationUserRepo.findByUsername(userName);
        if(user!=null){
            return new User(user.getUsername(), user.getPassword(), Collections.emptyList());
        }else{
            throw new UsernameNotFoundException(userName);
        }
    }
  }

如果想更改登陆的URL,可以在webConfig里面这样创建filter

.. code-block:: java
  
  final JWTAuthenticationFilter jwtAuthenticationFilter = new JWTAuthenticationFilter(authenticationManager());
  jwtAuthenticationFilter.setFilterProcessesUrl("/users/login");

Advance
--------------

Consider Authentication/Authorization with Roles, User groups.



Reference
-------------

* https://qtdebug.com/spring-security-8-token/
* https://auth0.com/blog/implementing-jwt-authentication-on-spring-boot/
* https://docs.spring.io/spring-security/site/docs/3.0.x/reference/el-access.html
* https://blog.csdn.net/u013435893/article/details/79605239
* https://github.com/ajkr195/springbootrocks
* https://github.com/ajkr195/SpringMvcSecurity4Hibernate5
* https://www.jianshu.com/p/18875c2995f1


.. index:: Security, Authentication, SpringBoot
