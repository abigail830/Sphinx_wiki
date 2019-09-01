3.3 怎样触发Controller
========================================

* `MockMVC`_
* `Rest-Assured`_
* `TestRestTemplate`_

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






.. index:: Testing, Junit
