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

这里使用的是postHandle而不是preHandle： 

* 方法一：如果使用preHandle的话，那应该在里面就需要拿到body里面的applicationUser信息，然后连接DB进行校验。只有校验通过之后才生成token。然后在对应的service里面就不需要再做校验的工作，只是继续比如保存audit trail。
  但是这种方式就牵涉到需要读@RequestBody的内容，因为是inputStream流通常只能读一次，除非在中间层再搞个wrapper之类的把inputStream缓存下来，通过Filter等方式继续把缓存的内容传递下去。

* 方法二：在拦截器里面不校验，由service里面进行校验,如果校验通过，后台会在response.header里面设置token所需的比如username, 然后拦截器在postHandle里面根据这个key生成token放回到response.Authorization里面给后续使用


.. code-block:: java
  
  @Component
  public class AuthenticationInterceptor implements HandlerInterceptor {

    @Autowired
    JWTAuth0Util jwtAuth0Util;

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, 
                        Object handler, @Nullable ModelAndView modelAndView) throws Exception {        
        if (handler instanceof HandlerMethod) {        
            HandlerMethod handlerMethod = (HandlerMethod) handler;
            Method method = handlerMethod.getMethod();
            if (method.isAnnotationPresent(UserLoginToken.class)) {
                final String usename = response.getHeader(HEADER_USERNAME);
                if (usename != null) {
                    String token = jwtAuth0Util.sign(usename);
                    response.setHeader(HEADER_AUTHORIZATION, token);
                }
            }
        }
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
