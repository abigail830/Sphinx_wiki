ArchUnit
==============

Dependency
----------------

pom.xml

.. code-block:: xml

    		<dependency>
			<groupId>com.tngtech.archunit</groupId>
			<artifactId>archunit</artifactId>
			<version>0.12.0</version>
			<scope>test</scope>
		</dependency>

		<!-- junit 4 -->
		<!--<dependency>-->
			<!--<groupId>com.tngtech.archunit</groupId>-->
			<!--<artifactId>archunit-junit4</artifactId>-->
			<!--<version>0.9.1</version>-->
			<!--<scope>test</scope>-->
		<!--</dependency>-->


Test case
--------------

With classes
^^^^^^^^^^^^^^^^^

分层策略： 检测某类文件是否在某个路径下

.. code-block:: java
  
    @Test
    public void controller_arch_rules() {
      JavaClasses importedClasses = new ClassFileImporter().importPackages("com.github.abigail830.mybatictest");
      
        ArchRule rule = classes().that().areAnnotatedWith(Controller.class)
                .or().areAnnotatedWith(RestController.class)
                .or().areAnnotatedWith(ControllerAdvice.class)
                .or().haveNameMatching(".*Controller")
                .should().resideInAPackage("..api..");

        rule.check(importedClasses);
    }

调用
调用关系



