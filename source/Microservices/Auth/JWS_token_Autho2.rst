JWT、Token的授权例子(2)
=============================

在例子1中，没有使用spring-Security，这个例子集中来看看如果使用了Spring-Security之后可以带来什么便利

代码摘要
-----------

* `pom.xml`_
* `JWTAuthorizationFilter`_
* `WebSecurityConfig`_

当POST+/register的时候，不会做校验，从而让用户信息可以存到DB。当/login的时候会进行Authentication（下篇再讲）。而任何其他url进来的话都会进行authorization。只要header中没有token或者token无法verify通过，则返回403.


pom.xml
^^^^^^^^^^

.. code-block:: xml
  
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
  </dependency>

JWTAuthorizationFilter
^^^^^^^^^^^^^^^^^^^^^^^^^^^

这里重载了BasicAuthenticationFilter，在默认的BasicAuthenticationFilter中，HEADER默认是Authorization, Prefix默认是basic。重载之后可以根据需要重新设定其他值。同时在getAuthentication方法里面可以自定义校验的方法，比如下面用的JWT.verify

.. code-block:: java
  
  public class JWTAuthorizationFilter extends BasicAuthenticationFilter {

    @Autowired
    JWTAuth0Util jwtAuth0Util;

    public JWTAuthorizationFilter(AuthenticationManager authenticationManager) {
        super(authenticationManager);
    }

    @Override
    protected void doFilterInternal(HttpServletRequest req,
                                    HttpServletResponse res,
                                    FilterChain chain) throws IOException, ServletException {
        String header = req.getHeader(HEADER_AUTHORIZATION);

        if (header == null || !header.startsWith(TOKEN_PREFIX)) {
            chain.doFilter(req, res);
            return;
        }

        UsernamePasswordAuthenticationToken authentication = getAuthentication(req);

        SecurityContextHolder.getContext().setAuthentication(authentication);
        chain.doFilter(req, res);
    }

    private UsernamePasswordAuthenticationToken getAuthentication(HttpServletRequest request) {
        String token = request.getHeader(HEADER_AUTHORIZATION);
        if (token != null) {
            // parse the token.
            final DecodedJWT decode = jwtAuth0Util.verify(token);
            String user = decode.getSubject();

            if (user != null) {
                return new UsernamePasswordAuthenticationToken(user, null, new ArrayList<>());
            }
            return null;
        }
        return null;
    }
  }

WebSecurityConfig
^^^^^^^^^^^^^^^^^^^^^^^^^

这里配置的是除了POST+REGISTER_URL之外，其他都需要authorizeRequests()

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
  }

.. index:: Security, Authorization, Spring
