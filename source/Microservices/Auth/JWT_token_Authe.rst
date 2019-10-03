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

正常的话，应该在里面就需要拿到body里面的applicationUser信息，然后连接DB进行校验。只有校验通过之后才生成token。然后在对应的service里面就不需要再做校验的工作，只是继续比如保存audit trail。这样在header里面也不需要有类似user信息这样额外的header

但是这种方式就牵涉到需要读@RequestBody的内容，因为是inputStream流通常只能读一次，不然会遇到exception
  HttpMessageNotReadableException: I/O error while reading input message; nested exception is java.io.IOException: Stream closed]
  
除非在中间层再搞个wrapper之类的把inputStream缓存下来，通过Filter等方式继续把缓存的内容传递下去。
参考： https://github.com/abigail830/jwt-demo/commit/dea76ed1bca49315efa0ba74fbc2b00d9d043625

即使这样做了，因为只override了getInputStream/getReader两个方法，controller当中也没办法使用@RequestBody了，也只能调用类似request.getReader/getParameter之类的方式，从而底层是使用了重载后的方法获取cache后的body。还是比较麻烦的。

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
