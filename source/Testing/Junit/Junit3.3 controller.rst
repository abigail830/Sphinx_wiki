3.3 怎样触发Controller
========================================

基本触发方式：

* `MockMVC`_
* `Rest-Assured`_
* `TestRestTemplate`_

延伸应用：

* `如果单独测试Controller层`_

------------------------------


MockMVC
--------------

mockMvc可以在同一个进程内模拟rest请求，如果测试使用了Transactional，mockMvc发送的请求可以在事务内进行回滚。

.. code-block:: java

  // skip some config for springExtension.class
  @AutoConfigureMockMvc
  class UserMockMvcIntegrationTest extends IntegrationTestBase {

    // skip some part for setup local random port
    
    @Autowired
    MockMvc mockMvc;
  
    @Test
    @DatabaseSetup(value = "/dbunit/UserTest_allUsers.xml", type = DatabaseOperation.CLEAN_INSERT)
    void getAllUsers() throws Exception {
        //when
        final MvcResult mvcResult = mockMvc.perform(get("/users")).andReturn();
        //then
        Assertions.assertEquals(200, mvcResult.getResponse().getStatus());
        String expect = "[" +
                "{\"id\":1,\"userName\":\"user1\",\"gender\":\"F\",\"avatarUrl\":\"url1\"}," +
                "{\"id\":2,\"userName\":\"user2\",\"gender\":\"M\",\"avatarUrl\":null}" +
                "]";
        Assertions.assertEquals(expect, mvcResult.getResponse().getContentAsString());
    }
  }


Rest-Assured
-----------------------

使用Given/When/Then的BDD写法，可以把结果extract().as(*.class), 也可以直接使用jsonPath进行比较

  “.body("userName", hasItems("user1", "user2"))”

.. code-block:: java
  
  public class UserRestAssuredIntegrationTest extends IntegrationTestBase {
  
    @Test
    @DatabaseSetup(value = "/dbunit/UserTest_allUsers.xml", type = DatabaseOperation.CLEAN_INSERT)
    void should_get_user_by_id() {
  
        final SimpleUserResponseDTO result = when()
                .get("/users/1")
                .then()
                .statusCode(HttpStatus.OK.value())
                .contentType(MediaType.APPLICATION_JSON_UTF8_VALUE)
                .extract()
                .as(SimpleUserResponseDTO.class);
  
        Assertions.assertEquals("url1", result.getAvatarUrl());
        Assertions.assertEquals("user1", result.getUserName());
        Assertions.assertEquals("F", result.getGender());
    }
  }



TestRestTemplate
---------------------------

与mockMvc比较，TestRestTemplate不是在同一个进程内，它是真的在模拟另一个外部服务通过controller调用。所以即使在测试中mark @Transactional，也没有办法回滚测试数据

.. code-block:: java
  
  public class UserRestTemplateIntegrationTest extends IntegrationTestBase {

    @Autowired
    TestRestTemplate testRestTemplate;

    @Test
    @DatabaseSetup(value = "/dbunit/UserTest_allUsers.xml", type = DatabaseOperation.CLEAN_INSERT)
    void should_get_all_users() {

        List<User> userList = new ArrayList<>();
        final ResponseEntity<? extends List> result = testRestTemplate
                .exchange("/users", HttpMethod.GET, null, userList.getClass());

        Assertions.assertEquals(HttpStatus.OK, result.getStatusCode());
        Assertions.assertEquals(2, result.getBody().size());
    }
  }

如果单独测试Controller层
----------------------------

可以利用MockBean把Controller所依赖的Service生成替身，正常Mockito写法，并准备好测试数据的模拟when/thenReturn.

.. code-block:: java
  
  @AutoConfigureMockMvc
  class UserMockBeanIntegrationTest extends IntegrationTestBase {

    @Autowired
    MockMvc mockMvc;

    @MockBean
    UserService userService;

    @BeforeEach
    public void setUp() {
        super.setUp();
        initMocks(this);
    }

    @Test
    void getAllUsers() throws Exception {
        //given
        List<User> users = Arrays.asList(new User(1, "user1", "M", "url1"));
        when(userService.getAllUsers()).thenReturn(users);
        //when
        final MvcResult mvcResult = mockMvc.perform(get("/users")).andReturn();
        //then
        Assertions.assertEquals(200, mvcResult.getResponse().getStatus());

        String expect = "[{\"id\":1,\"userName\":\"user1\",\"gender\":\"M\",\"avatarUrl\":\"url1\"}]";
        Assertions.assertEquals(expect, mvcResult.getResponse().getContentAsString());
    }
  }

.. index:: Testing, Junit
