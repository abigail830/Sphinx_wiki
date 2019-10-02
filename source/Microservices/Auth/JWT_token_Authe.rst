JWS、Token的认证例子（1）
==========================

该方式使用 Annotation + Interceptor + SpringBoot 为例子, 关注在如何进行认证（验证客户信息和授予token）其中没有使用Spring Security，使用了全局secret

基本操作
-------------

* `UserLoginToken`_
* `AuthenticationInterceptor`_
* `InterceptorConfig`_

调用例子
----------

* `UserController`_

----------------------

UserLoginToken
^^^^^^^^^^^^^^^^^^^^^

.. code-block:: java
  
  @Target({ElementType.TYPE, ElementType.METHOD})
  @Retention(RetentionPolicy.RUNTIME)
  public @interface UserLoginToken {
  //    boolean required() default true;
  }


AuthenticationInterceptor
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: java
  
  @Component
  public class AuthenticationInterceptor implements HandlerInterceptor {

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

        if (method.isAnnotationPresent(UserLoginToken.class)) {
            final String usename = request.getHeader(HEADER_USERNAME);
            if (usename != null) {
                try {
                    String token = jwtAuth0Util.sign(usename);
                    response.setHeader(HEADER_AUTHORIZATION, token);
                } catch (JWTCreationException ex) {
                    return failToSignTokenResponse(response);
                }
            } else {
                return failToSignTokenResponse(response);
            }

        }
        return true;
    }

    private boolean failToSignTokenResponse(HttpServletResponse response) throws IOException {
        response.getWriter().write("Fail to sign token.");
        response.setContentType("application/json");
        response.setStatus(HttpServletResponse.SC_INTERNAL_SERVER_ERROR);
        return false;
    }
  }

InterceptorConfig
^^^^^^^^^^^^^^^^^^^^^

.. code-block:: java
  
  @Configuration
  public class InterceptorConfig implements WebMvcConfigurer {

    @Autowired
    AuthorizationInterceptor authorizationInterceptor;

    @Autowired
    AuthenticationInterceptor authenticationInterceptor;

    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(authenticationInterceptor);
        registry.addInterceptor(authorizationInterceptor);
    }
  }

UserController
^^^^^^^^^^^^^^^^^^^^

可以看到这里不同的控制：

* 对于注册来说，不需要授权，不需要生成token，让新用户可以进入，（这里假设注册之后用户还需要手动输入信息登陆一次）
* 对于登陆来说，不需要授权，但会生成toekn
* 对于查询来说，需要授权通过，不需要生成token

.. code-block:: java
  
  @RestController
  @RequestMapping(value = "/users")
  public class UserController {

    @Autowired
    UserApplService userApplService;

    @PostMapping("/register")
    @IgnoreToken
    public void register(@RequestBody ApplicationUser user) {
        userApplService.register(user);
    }

    @PostMapping("/login")
    @IgnoreToken
    @UserLoginToken
    public void login(@RequestBody ApplicationUser user) {
        userApplService.saveUserLoginInfo(user);
    }

    @GetMapping
    public ApplicationUser findUser(@RequestParam String userName) {
        return userApplService.findUserByName(userName);
    }
  }

.. index:: JWT, Authentication
