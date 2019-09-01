3.2 怎样准备测试数据
==========================


方法一 利用程序Repository、Dao中写好的CRUD
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  
如，先使用insert方法插入测试数据，测试后，使用delete方法删除数据。开发简单但不严谨，如果出错了，CRUD中的任意一个方法都可能是原因。适合刚开始阶段为了更多的速度而进行冒烟测试。

方法二 使用独立的JDBC+SQL准备和清理数据
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

在测试过程中，利用JdbcTemplate, 使用独立的SQL准备/删除数据。需要另外准备SQL和JdbcTemplate部分的程序，开发上较为繁琐。


方法三 使用三方工具如DbUnit、Db-Rider等
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

借用三方框架，开发简洁，但需要先熟悉三方库的使用方式。常用工具如：Dbunit, DB-rider等。

DbUnit
+++++++++++++

pom.xml

.. code-block:: xml
  
  <dependency>
    <groupId>org.dbunit</groupId>
    <artifactId>dbunit</artifactId>
    <version>2.5.0</version>
    <type>jar</type>
  <scope>test</scope>
  </dependency>
  <dependency>
    <groupId>com.github.springtestdbunit</groupId>
    <artifactId>spring-test-dbunit</artifactId>
    <version>1.2.0</version>
    <scope>test</scope>
  </dependency>

test/resource/dbunit/UserTest_allUsers.xml

.. code-block:: xml
  
  <dataset>
    <user_tbl id="1" user_name="user1" gender="F" avatar_url="url1"></user_tbl>
    <user_tbl id="2" user_name="user2" gender="M"></user_tbl>
    <wish_tbl id="1" user_id="1" description="This is wish1" create_time="2018-12-12 12:12:12.112233445"></wish_tbl>
    <wish_tbl id="2" user_id="1" description="This is wish2" create_time="2018-12-13 12:12:12.112233445"></wish_tbl>
    <wish_tbl id="3" user_id="2" description="This is wish3" create_time="2019-01-11 12:12:12.112233445"></wish_tbl>
  </dataset>

.. code-block:: java

  @ExtendWith(SpringExtension.class)
  @SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT, classes = MybaticTestApplication.class)
  @TestExecutionListeners({DependencyInjectionTestExecutionListener.class,
        DirtiesContextTestExecutionListener.class,
        TransactionalTestExecutionListener.class,
        DbUnitTestExecutionListener.class,
        MockitoTestExecutionListener.class})
  public class IntegrationTestBase {
    
    @LocalServerPort
    private int port;

    @BeforeEach
    public void setUp() {
        RestAssured.port = port;
    }
  
    @Test
    @DatabaseSetup(value = "/dbunit/UserTest_allUsers.xml", type = DatabaseOperation.CLEAN_INSERT)
    @ExpectedDatabase(value = "/dbunit/UserTest_insertUser_expect.xml",
            table = "user_tbl",
            assertionMode = DatabaseAssertionMode.NON_STRICT)
    @DatabaseTearDown(value = "/dbunit/UserTest_insertUser_expect.xml", type = DatabaseOperation.DELETE)
    void should_add_user() {
        //given
        final User user3 = new User("user3", "M", "url3");
        //when
        userService.addUser(user3);
        //then
        //assert result is in above xml config
    }
  }

DB-rider
+++++++++++++++++

pom.xml

.. code-block:: xml
  
  <dependency>
    <groupId>com.github.database-rider</groupId>
    <artifactId>rider-spring</artifactId>
    <version>1.5.2</version>
    <scope>test</scope>
    <exclusions>
      <exclusion>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
      </exclusion>
    </exclusions>
  </dependency>

test/resources/db_rider/allUser.yaml, DB-Rider支持更多格式的数据准备

.. code-block:: yaml
  
  user_tbl:
  - id: 1
    user_name: "user1"
    gender: "F"
    avatar_url: "url1"
  - id: 2
    user_name: "user2"
    gender: "M"

test java

.. code-block:: java
  
  @SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT, classes = MybaticTestApplication.class)
  @DBRider
  class WishInfrastructureIntegrationDBRiderTest{
  
    @Autowired
    WishInfrastructure wishInfrastructure;

    @Test
    @DataSet(value = "/db_rider/allUser.yml",
            strategy = SeedStrategy.CLEAN_INSERT,
            cleanAfter = true)
    void getWishById() {
        //when
        final Wish wish3 = wishInfrastructure.getWishById(3).get();

        //then
        Assertions.assertEquals("This is wish3", wish3.getDescription());
        Assertions.assertEquals("2019-01-11 12:12:12.112233", wish3.getCreateTime().toString());
    }
  }

