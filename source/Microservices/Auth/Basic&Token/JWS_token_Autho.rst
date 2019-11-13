JWS、Token的授权例子（1）
=====================================

该方式使用 Annotation + Interceptor + SpringBoot 为例子, 关注在如何进行授权（假设用户已经有必要的机制做验证和生成JWT token）其中没有使用Spring Security，使用了全局secret


基本步骤
-------------

* `Annotation: IgnoreToken.java`_
* `Interceptor: AuthorizationInterceptor.java`_
* `InterceptorConfig: InterceptorConfig.java`_
* `Infrastructure: JWTAuth0Util.java`_

调用步骤
-------------

* `Controller(IgnoreToken): HelloController.java`_
* `Controller(No IgnoreToken): UserController.java`_


Annotation: IgnoreToken.java
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Annotation设置为可以使用在方法级，必要时还可以给这个Annotation设置不同的flag进行控制，但这里只需要简单的当作有Annotation默认就是Ignore就好了。

.. code-block:: java
  
  @Target({ElementType.TYPE, ElementType.METHOD})
  @Retention(RetentionPolicy.RUNTIME)
  public @interface IgnoreToken {
  //    boolean required() default true;
  }

Interceptor: AuthorizationInterceptor.java
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

这里作为例子,使用了全局的Secret并只作了最基础的两项检查：

* header中是否包含"Authorization:token"
* 获取token并进行JWT验证

  这种方式后台完全没有状态，也不需要进一步查询，所以效率比较高。需要注意的是可以考虑设置一个比较短的token有效时间，和更频密的更新这个全局的secret能让授权更安全。

如需要还可以有更多的操作，比如说：

* 首先decode JWT，获取如用户名的信息
* 根据用户名（从DB或redis）查询到对应的密码或者Secret
* 根据获取的password或者secret进行verify

  这种方式就需要在后台有存储状态（如username/secret之间），回到了类似cookie/session的状况

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

InterceptorConfig: InterceptorConfig.java
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: java
    
  @Configuration
  public class InterceptorConfig implements WebMvcConfigurer {
    
      @Autowired
      AuthorizationInterceptor authorizationInterceptor;
  
      public void addInterceptors(InterceptorRegistry registry) {
          registry.addInterceptor(authorizationInterceptor);
      }
  }


Infrastructure: JWTAuth0Util.java
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

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


Controller(IgnoreToken): HelloController.java
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

配合Annotation使用，所以当用户调用 GET /hello 的时候，服务都不会校验Header当中的Token，而直接可以返回“Hello World".

.. code-block:: java
  
  @RestController
  @RequestMapping(value = "/hello")
  public class HelloController {

    @Autowired
    UserApplService userApplService;

    @GetMapping
    @IgnoreToken
    public String hello() {
        return "Hello World";
    }
  }


Controller(No IgnoreToken): UserController.java
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

这时候，当用户调用 GET /users，interceptor就会校验header中是否有token，有的话进一步校验，如果任意一处不通过，则不会返回用户查询结果

.. code-block:: java
  
  @RestController
  @RequestMapping(value = "/users")
  public class UserController {
  
    @Autowired
    UserApplService userApplService;

    @GetMapping
    public ApplicationUser findUser(@RequestParam String userName) {
        return userApplService.findUserByName(userName);
    }
  }


另外，因为我们还可以把UserGroup的信息放在Token里面，如果token验证通过后是否马上可以获取所需信息, 还可以再添加另外一层的interceptor专门做User Group相关的授权认证。因为签名已经验过，在UserGroup授权的时候，只需要decode了token获取用户组，然后查询DB或Redis等基础设施获取用户组与页面或对应信息的权限关系。


.. index:: Authorization, Security, JWT, Token

  

