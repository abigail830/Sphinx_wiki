3. 组件测试
==========================

*  `什么是组件测试？`_



什么是组件测试？
------------------

* 不但只针对某一个类/方法的，可能联通了几个类/方法一起进行测试
* 可能涉及连接数据库/三方服务的逻辑
* 如在spring中，从controller都service到dao到数据库进行通测。


.. image:: ../../../images/component_test.png
  :width: 500px



命名规范
----------------

通常，单元测试的案例会放在和原被测试文件同样的package路径下（不过是under test), 但对于组件测试，我们需要考虑更多：

* 组件测试运行时间较长，当案例越来越多的时候，在某些场景下，我们可能会想单独运行单元测试，或者单独运行组件测试。
* 组件测试更多的为了测试某一个场景，如果使用跟单测一样的命名方式，可能并没有把场景描述清晰。

所以，几种思路

1. 放在新建独立目录：src/integrationtest, 运行的时候就指定这个路径，为了流水线运行，在plugin也需要特别指定
  
.. code-block:: xml
  
              <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>build-helper-maven-plugin</artifactId>
                <version>3.0.0</version>
                <executions>
                    <execution>
                        <id>add-integration-test-source</id>
                        <phase>generate-test-sources</phase>
                        <goals>
                            <goal>add-test-source</goal>
                        </goals>
                        <configuration>
                            <sources>
                                <source>src/integrationtest/java</source>
                            </sources>
                        </configuration>
                    </execution>
                    <execution>
                        <id>add-integration-test-resource</id>
                        <phase>generate-test-resources</phase>
                        <goals>
                            <goal>add-test-resource</goal>
                        </goals>
                        <configuration>
                            <resources>
                                <resource>
                                    <directory>src/integrationtest/resources</directory>
                                </resource>
                            </resources>
                        </configuration>
                    </execution>
                </executions>
            </plugin>

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.0.0-M3</version>
                <configuration>
                    <skipTests>${skipUnitTest}</skipTests>
                </configuration>
            </plugin>


            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-failsafe-plugin</artifactId>
                <version>3.0.0-M3</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>integration-test</goal>
                            <goal>verify</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>

2. 所有组件测试以IT结尾，和单元测试放在一样的路径规范下，运行时，使用pattern。如“^(?!.*IT).*$”表示所有不是IT结尾的测试案例。

3. 创建IntegrationTestBase,标记好tag是Integraton（可改），然后其他组件测试都extends这个基类。而在pipeline运行的时候也可以指定-Dtag=Integration，这个好处是可以把很多通用Annotation也放在父类。

.. code-block:: java
  
  @ExtendWith(SpringExtension.class)
  @SpringBootTest
  @Tag("Integration")
  public class IntegrationTestBase {
  
  }

另关于文件命名，以测试案例描述的那个场景然后加“IT”结尾。如CustomerServiceByJdbcIT.java


.. index:: Testing, Junit
