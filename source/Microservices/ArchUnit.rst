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


分层策略检查： 某类文件是否在某个路径下
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

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

调用关系检查：只能通过interface调用
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: java
	
    @Test
    void repo_implement_should_only_be_access_via_interface() {
        final ArchRule rule = classes().that().haveNameMatching("InfrastructureImpl")
                .should().onlyBeAccessed().byAnyPackage()
                .because("repo_implement_should_only_be_access_via_interface");
        rule.check(importedClasses);
    }

调用关系检查：分层之间的调用关系
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: java
	
    @Test
    void layer_rules() {
        final Architectures.LayeredArchitecture layeredArchitecture = layeredArchitecture()
                .layer("Controller").definedBy("com.github.abigail830.mybatictest.api")
                .layer("Service").definedBy("com.github.abigail830.mybatictest.domain")
                .layer("Infra").definedBy("com.github.abigail830.mybatictest.infrastructure")
                .whereLayer("Controller").mayNotBeAccessedByAnyLayer()
                .whereLayer("Service").mayOnlyBeAccessedByLayers("Controller")
                .whereLayer("Infra").mayOnlyBeAccessedByLayers("Service");

        layeredArchitecture.check(importedClasses);
    }

如果出错，会报警。如：
	
	java.lang.AssertionError: Architecture Violation [Priority: MEDIUM] - Rule 'Layered architecture consisting of	

	layer 'Controller' ('com.github.abigail830.mybatictest.api')
	layer 'Service' ('com.github.abigail830.mybatictest.domain')
	layer 'Infra' ('com.github.abigail830.mybatictest.infrastructure')
	
	where layer 'Controller' may not be accessed by any layer
	where layer 'Service' may only be accessed by layers ['Controller']
	where layer 'Infra' may only be accessed by layers ['Service']' was violated (2 times):
	
	Class <com.github.abigail830.mybatictest.infrastructure.UserInfrastructureImpl> implements interface 	<com.github.abigail830.mybatictest.domain.UserInfrastructure> in (UserInfrastructureImpl.java:0)
	
	Class <com.github.abigail830.mybatictest.infrastructure.WishInfrastructureImp> implements interface 	<com.github.abigail830.mybatictest.domain.WishInfrastructure> in (WishInfrastructureImp.java:0)


类之间的依赖检查: 无依赖 & 无循环依赖
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: java
	
    @Test
    void controllers_should_not_dependend_each_other() {
        final SliceRule rule = SlicesRuleDefinition
                .slices().matching(".controller.(**)")
                .should().notDependOnEachOther();
        rule.check(importedClasses);
    }

    @Test
    void services_should_not_have_cyclic_dependencies() {
        final SliceRule rule = SlicesRuleDefinition
                .slices().matching(".service.(**)")
                .should()
                .beFreeOfCycles();
        rule.check(importedClasses);
    }

.. index:: ArchUnit

