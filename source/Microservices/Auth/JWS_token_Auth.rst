基于JWS、Token的Authorization（1）
=====================================

该方式使用 Annotation + Interceptor + SpringBoot 为例子

**Annotation:** IgnoreToken.java

.. code-block:: java
  
  @Target({ElementType.TYPE, ElementType.METHOD})
  @Retention(RetentionPolicy.RUNTIME)
  public @interface IgnoreToken {
  //    boolean required() default true;
  }

**Interceptor:** AuthorizationInterceptor.java

.. code-block:: java
  
  @Component
  public class AuthorizationInterceptor implements HandlerInterceptor {

    @Autowired
    JWTAuth0Util jwtAuth0Util;
  
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
                             Object handler) throws Exception {
  
        if (!(handler instanceof HandlerMethod)) {
            return true;
        }
  
        HandlerMethod handlerMethod = (HandlerMethod) handler;
        Method method = handlerMethod.getMethod();
  
        if (method.isAnnotationPresent(IgnoreToken.class)) {
            return true;
        } else {
            String token = request.getHeader(HEADER_STRING);
            if (token == null) {
                response.getWriter().write("No Token, please login first.");
                response.setStatus(HttpServletResponse.SC_BAD_REQUEST);
                return false;
            } else {
                try {
                    jwtAuth0Util.verify(token);
                } catch (JWTVerificationException exception) {
                    response.getWriter().write("Fail to verify token.");
                    response.setContentType("application/json");
                    response.setStatus(HttpServletResponse.SC_FORBIDDEN);
                    return false;
                }
                return true;
            }
        }
    }
  
  }

**InterceptorConfig:** InterceptorConfig.java

  .. code-block:: java
    
    @Configuration
    public class InterceptorConfig implements WebMvcConfigurer {
    
      @Autowired
      AuthorizationInterceptor authorizationInterceptor;
  
      public void addInterceptors(InterceptorRegistry registry) {
          registry.addInterceptor(authorizationInterceptor);
      }
  }


**Infrastructure:** JWTAuth0Util.java

.. code-block:: java
  
  @Component
  public class JWTAuth0Util {
  
    private static final String SECRET = "Secret";
  
    public String sign(ApplicationUser user) throws JWTCreationException {
        return JWT.create()
                .withSubject(user.getUsername())
                .withIssuer("jwt-demo")
                .withExpiresAt(new Date(System.currentTimeMillis() + EXPIRATION_TIME))
                .sign(Algorithm.HMAC512(SECRET));
    }
  
    public DecodedJWT verify(String token) throws JWTVerificationException {
        Algorithm algorithm = Algorithm.HMAC512(SECRET);
        JWTVerifier verifier = JWT.require(algorithm)
                .withIssuer("jwt-demo")
                .build();
        return verifier.verify(token);
    }
  
    public DecodedJWT decode(String token) throws JWTDecodeException {
        return JWT.decode(token);
    }
  }


.. index:: Authorization, Security, JWT, Token

  

