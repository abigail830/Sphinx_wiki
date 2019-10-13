1.3 Config Spring Project
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

Default path: src/test/resources/contracts， 因为测试需要对应数据准备，所以通常还会再建目录结构，这就需要配置指定testJava如何与contract匹配的问题。 例如，

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

**示例代码**: 这里使用了mock的方式, 最后一行配置

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



Step 4
^^^^^^^^^

此时只要运行mvn test或者gradle build

.. index:: Testing, Contract


