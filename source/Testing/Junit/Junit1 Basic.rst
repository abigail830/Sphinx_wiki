1. Junit Basic
================

每个测试方法应该按照用户故事中的验收提交分为 Given\When\Then 三段。如：

.. code-block:: java

  @Test
    void should_return_parking_ticket_when_have_parkinglots_available() throws Exception {
        // Given 设置前置条件
        List<ParkingLot> parkingLots = new ArrayList<>();
        parkingLots.add(new ParkingLot(10));
        SmartParkingBoy parkingBoy = new SmartParkingBoy(parkingLots);

        // When 执行被测方法
        Ticket ticket = parkingBoy.park(new Car());

        // Then 验证方法结果
        assertThat(ticket).isNotNull();
    }


1.1 Prepare Step - 前置准备（Given）
-----------------------------------

不同的准备方法，如：

* 直接创建需要的Object（如上）
* 使用Mock的方式创建Object并配置好假设数据
* 如与数据库有关，可以使用DBUnit或者H2预先导入测试数据
* 提前读取/创建所需文件/文件夹，可以考虑使用@Rule方式配置TemporaryFolder

可以在不同的位置配置，如：

* 在每个测试方法里面配置（如上）
* 在@Before/@BeforeEach的setup方法里面配置，每个测试方法运行前会自动运行，用完后可以在@After/@AfterEach里面销毁
* 在@BeforeClass/@BeforeAll的方法里面配置，整个测试类创建时候会自动运行，用完后可以在@AfterClass/@AfterAll方法里面销毁

1.2. Execution - 执行方法（When）
----------------------------------

把需要测试的代码触发一次

1.3. Assert Result - 判断结果（Then）
------------------------------------

多种不同类型的判断

* Junit原生的断言

.. code-block:: java

  Assert.assertEquals("expect", actual);
  Assert.assertEquals(50, progress);
  Assert.assertTrue(boolean);

* Hamcrest的断言

.. code-block:: java

  assertThat(theBiscuit, equalTo(myBiscuit));
  assertThat("chocolate chips", theBiscuit.getChocolateChipCount(),equalTo(10));
  assertThat("myname", allOf(startsWith("my"), containsString("name")));
  assertThat(Arrays.asList("my", "mine"), everyItem(startsWith("m")));
  assertThat(str, is(nullValue()));
  assertThat("myname", stringContainsInOrder(Arrays.asList("my", "me")));

  
Reference:
  * http://hamcrest.org/JavaHamcrest/tutorial
  * https://blog.csdn.net/neven7/article/details/42489723
  
* AssertJ的断言

.. code-block:: java

  assertThat(formIDs.size(), is(0));
  assertThat(firstParkingLot.contains(ticket)).isTrue();
  assertThat(ticket).isNotNull();
  assertThat(takenCar).isEqualTo(car);
  assertThatThrownBy(() -> parkingBoy.park(new Car())).isInstanceOf(AllParkingLotsIsFullException.class);

Reference: 
  * http://joel-costigliola.github.io/assertj/

.. index:: Testing
