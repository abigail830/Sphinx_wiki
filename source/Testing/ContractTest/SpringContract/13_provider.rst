1.3 Provider
============================

Adding dependency and plugin
---------------------------------

pom.xml

.. code-block:: xml
  
  <dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-contract-verifier</artifactId>
    <scope>test</scope>
  </dependency>

  <plugin>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-contract-maven-plugin</artifactId>
    <version>${spring-cloud-contract.version}</version>
    <extensions>true</extensions>
  </plugin>


build.gradle

.. code-block:: groovy
  
  buildscript {
    dependencies {
        classpath('org.springframework.cloud:spring-cloud-contract-gradle-plugin:2.0.1.RELEASE')
    }  
  }
  ext {
    dependencies {
        testImplementation('org.springframework.cloud:spring-cloud-starter-contract-verifier')
    }
}



Write contract
-----------------

准确来说这里是放置契约。契约应该在之前已经经过双方协商定下来了，可以由任意一方把它落到文件格式。 契约默认放置路径: src/test/resources/contracts

因为测试需要对应数据准备，所以通常还会再建目录结构，这就需要配置指定testJava如何与contract匹配的问题。 例如，

Step 1
^^^^^^^

创建并编写契约/api/product/should_created_product.yml， 没有放在默认路径，而在项目根目录下

.. code-block:: yaml
  
  request:
    method: POST
    url: /products
    headers:
      Content-Type: application/json
    body: |
      {
        "name": "Mac mini",
        "description": "Mac mini computer",
        "price": "9888.00"
      }
  response:
    status: 200
    headers:
      Content-Type: application/json;charset=UTF-8
    body: |
      {
        "id": 10,
        "name": "Mac mini",
        "description": "Mac mini computer",
        "price": "9888.00"
      }



Step 2
^^^^^^^^

build.gradle中配置: 

* **contractsDslDir:** 指定契约所放的目录，如这里是/api, 所以 project.file("api")
* **packageWithBaseClasses:** 指定对应Java测试Base放的目录，所以这里对应的就是src/test/java/com.dmall.productservice.contractTest

.. code-block:: groovy
  
  contracts {
        packageWithBaseClasses = 'com.dmall.productservice.contractTest'
        contractsDslDir = project.file("api")
  }

Step 3
^^^^^^^^

在src/test/java/com.dmall.productservice.contractTest下，创建ProductBase.java, Product是对应了契约所在的sub folder, Base是固定命名，所以所有在/api/product下的契约将与这个Base对应。也就是说所有所需的数据都会在这个Base之中准备。 

**示例代码**: 

.. code-block:: java
  
  @Ignore
  @AutoConfigureMockMvc
  public class ProductBase {
  
    @Autowired
    WebApplicationContext wac;

    @Before
    public void setUp() throws Exception {
        final ProductService productService = mock(ProductService.class);
        final ProductAssembler productAssembler = mock(ProductAssembler.class);

        Product product1 = new Product(10L, "Mac mini", "Mac mini computer", new BigDecimal(9888.00));
        Product product2 = new Product(20L, "iPhone X", "iPhone 2018 new fashion", new BigDecimal(8888.00));
        List<Product> products = Arrays.asList(product1, product2);
        ProductResponse response1 = new ProductResponse();
        response1.setId(10L);
        response1.setName("Mac mini");
        response1.setDescription("Mac mini computer");
        response1.setPrice(new BigDecimal(9888.00));
        ProductResponse response2 = new ProductResponse();
        response2.setId(20L);
        response2.setName("iPhone X");
        response2.setDescription("iPhone 2018 new fashion");
        response2.setPrice(new BigDecimal(8888.00));

        when(productService.getProducts()).thenReturn(products);
        when(productService.getProductsById(anyLong())).thenReturn(product1);
        when(productService.save(any(Product.class))).thenReturn(product1);
        when(productAssembler.toDomainObject(any(ProductCreationRequest.class))).thenReturn(product1);
        when(productAssembler.toProductResponse(any(Product.class))).thenReturn(response1);
        when(productAssembler.toProductResponseList(anyList())).thenReturn(Arrays.asList(response1, response2));

        RestAssuredMockMvc.standaloneSetup(new ProductController(productService, productAssembler));
    }
  }

注意
`````
* 最后一行配置了对应的controller, 而且该controller是使用构造函数注入的，如果是使用autowire注入的话，应该在开始时候使用@InjectMocks的方式注入
* 这里使用了mock的方式, 所以所有数据通过mock返回，等于只测试了controller层的接口。
* 另一种思路是把它作为集成测试一直通测到DB实现，这是就可以使用类似DB-Rider/DB unit之类的工具帮助准备DB数据了。至于，应该只测接口还是通测，这是一个一直还在争论中的问题，需要根据实际场景探索寻找适合的平衡点。

Step 4
^^^^^^^^^

此时只要运行mvn test或者gradle build，就会触发到Spring Contract Test. 如果成功运行自然就build success了，如果失败，日志可能如下：

**失败日志**

  org.springframework.cloud.contract.verifier.tests.ProductTest > validate_should_get_products FAILED
    java.lang.IllegalStateException at ProductTest.java:80

**自动生成测试**

在这个过程之中，Spring其实会自动的根据契约生成测试案例，放置在build/generated-test-sources/contracts/org.springframework.cloud.contract.verifier.tests/ProductTest.java, 如果maven项目则在target目录下

.. code-block:: java
  
  public class ProductTest extends ProductBase {
  
	  @Test
  	public void validate_should_created_product() throws Exception {
	  	// given:
		  	MockMvcRequestSpecification request = given()
			  		.header("Content-Type", "application/json")
				  	.body("{\"name\":\"Mac mini\",\"description\":\"Mac mini computer\",\"price\":\"9888.00\"}");
	  	// when:
		  	ResponseOptions response = given().spec(request)
			  		.post("/products");
	  	// then:
		  	assertThat(response.statusCode()).isEqualTo(200);
			  assertThat(response.header("Content-Type")).isEqualTo("application/json;charset=UTF-8");
  		// and:
	  		DocumentContext parsedJson = JsonPath.parse(response.getBody().asString());
		  	assertThatJson(parsedJson).field("['id']").isEqualTo(10);
			  assertThatJson(parsedJson).field("['price']").isEqualTo("9888.00");
  			assertThatJson(parsedJson).field("['description']").isEqualTo("Mac mini computer");
	  		assertThatJson(parsedJson).field("['name']").isEqualTo("Mac mini");
  	}
  }

对照着step1中的契约内容，可以看见自动生成的测试：

* 基于MovkMvc，以validate+契约文件名作为案例名称，如果product文件夹下面有多个契约，都会生成在这个测试java里面成为多个测试案例。
* 继承了所对应的ProductBase
* 为契约中有的字段都写了断言，因为是hardcode的值，所以都是精确的isEqualTo。


Step 5
^^^^^^^^^^

除了自动生成测试案例，之前引入的spring-cloud-contract-maven-plugin还可以帮助生成Stub的挡板服务。具体参考https://cloud.spring.io/spring-cloud-contract/spring-cloud-contract-maven-plugin/plugin-info.html

这个plugin其实有几个步骤：

1. **convert**

把契约复制到build/stubs/META-INF/com.dmall/product-service/0.1.0/contracts/product下面，并且把内容转换为json放置在build/stubs/META-INF/com.dmall/product-service/0.1.0/contracts/mappings/product下面，这个json其实就是类似wiremock支持的json格式

2. **generateStubs**

这一步就会把之前生成出来json封装为一个基于wiremock的挡板服务jar包中，放置在build/lib.product-service-0.1.0-stub.jar, 后续只要上传到nexus或者jfrog之类的软件仓库就可以被任何需要该挡板的人所获取

3. **generateTests**

这个就是Step4中自动生成的测试

对生产者来说，关键是在gengerateTests。而是否应该在生产者里面去生成Stub这个可以灵活配置。

比如说，可以另外有一个mock-Server的项目，里面就是获取所有契约之后统一生成stub.jar的，也是可以的。这样的话在生产者的配置里面可以把convert和generateStubs这两个步骤skip掉，同理在mock-Server的项目里面，把generateTests的步骤skip掉


.. index:: Testing, Contract


