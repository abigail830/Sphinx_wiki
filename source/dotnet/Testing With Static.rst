Testing with static
===================================

假设场景，BizService中，调用了静态方法ConfigUtil.getDatabaseType()，该如何编写测试？

.. code-block:: c#
  
  using System;
  namespace staticdemo.service
  {
    public class BizService
    {
        private static string databaseType = ConfigUtil.getDatabaseType();

        public BizService()
        {
        }

        public void process()
        {
            if (databaseType.Equals("SQL"))
            {
                handleSQL();
            }
            else
            {
                throw new ArgumentException("Not support non-SQL");
            }
        }

        public void handleSQL()
        {
            throw new NotImplementedException();
        }
    }
  }

ConfigUtil代码如下

.. code-block:: c#
  
  using System;
  namespace staticdemo.service
  {
    public class ConfigUtil
    {
        public ConfigUtil()
        {
        }

        public static string getDatabaseType()
        {
            return "This is the result from static method";
        }
    }
  }

思路一: 抽取static为方法，然后mock了这个方法
-------------------------------------------------------

在BizService.cs中抽取方法

.. code-block:: c#
 
  using System;
  namespace staticdemo.service
  {
    public class BizService
    {
        //...constructor

        public void process()
        {
            if (getDatabaseType().Equals("SQL"))
            {
                handleSQL();
            }
            else
            {
                throw new ArgumentException("Not support non-SQL");
            }
        }

        public virtual string getDatabaseType()
        {
            return ConfigUtil.getDatabaseType();
        }
        
        //other method
    }
  }

在测试中, BizServiceTest.cs

.. code-block:: c#
  
  using Moq;
  using NUnit.Framework;
  using staticdemo.service;
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Text;
  using System.Web.Mvc;


  namespace staticdemo.Tests.service
  {
    [TestFixture]
    public class BizServiceTest
    {
        [Test]
        public void Test_Process_Method_databaseType_SQL()
        {
            // given
            Mock<BizService> spyService = new Mock<BizService>() { CallBase = true };
            spyService.Setup(s => s.handleSQL()).Verifiable();
            spyService.Setup(s => s.getDatabaseType()).Returns("SQL");

            //when
            spyService.Object.process();

            //then
            spyService.Verify(s => s.handleSQL(), Times.Once);
        }

        [Test]
        public void Test_Process_Method_databaseType_non_SQL()
        {
            // given
            Mock<BizService> spyService = new Mock<BizService>() { CallBase = true };
            spyService.Setup(s => s.handleSQL()).Verifiable();
            spyService.Setup(s => s.getDatabaseType()).Returns("ABC");

            //when
            var ex = Assert.Throws<ArgumentException>(() => spyService.Object.process());

            //then
            Assert.That(ex.Message, Is.EqualTo("Not support non-SQL"));
        }
    }
  }

注意，这里使用了virtual，是因为moq只能setup准备virtua的方法，所以

* 方法一： 直接抽取成virtual方法，如上
* 方法二： 把static调用抽取一个接口或中介类， 如下


思路二：把static抽取为factory（中间多一层）
-----------------------------------------------------------

ConfigFactory.cs, 里面调用ConfigUtil

.. code-block:: c#
  
  using System;
  using staticdemo.service;

  namespace staticdemo.util
  {
    public class ConfigFactory
    {
        public ConfigFactory()
        {
        }

        public virtual string GetDatabaseType()
        {
                return ConfigUtil.getDatabaseType();   
        }      
    }
  }

BizService.cs中，从构造器传入ConfigFactory

.. code-block:: c#
  
  using System;
  using staticdemo.util;

  namespace staticdemo.service
  {
    public class BizService
    {
        private ConfigFactory ConfFactory { get; set; }

        public BizService(ConfigFactory configFactory)
        {
            ConfFactory = configFactory;
        }

        public void Process()
        {
            if (ConfFactory.GetDatabaseType().Equals("SQL"))
            {
                HandleSQL();
            }
            else
            {
                throw new ArgumentException("Not support non-SQL");
            }
        }
        // other methods
    }
  }

BizServiceTest.cs

.. code-block:: c#
  
  using Moq;
  using NUnit.Framework;
  using staticdemo.service;
  using staticdemo.util;
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Text;
  using System.Web.Mvc;


  namespace staticdemo.Tests.service
  {
    [TestFixture]
    public class BizServiceTest
    {
        [Test]
        public void Test_Process_Method_databaseType_SQL()
        {
            // given
            Mock<ConfigFactory> mockConfigFactory = new Mock<ConfigFactory>();
            mockConfigFactory.Setup(cf => cf.GetDatabaseType()).Returns("SQL");

            Mock<BizService> spyService = new Mock<BizService>(mockConfigFactory.Object) { CallBase = true };
            spyService.Setup(s => s.HandleSQL()).Verifiable();

            //when
            spyService.Object.Process();

            //then
            spyService.Verify(s => s.HandleSQL(), Times.Once);
        }

        [Test]
        public void Test_Process_Method_databaseType_non_SQL()
        {
            // given
            Mock<ConfigFactory> mockConfigFactory = new Mock<ConfigFactory>();
            mockConfigFactory.Setup(cf => cf.GetDatabaseType()).Returns("ABC");

            Mock<BizService> spyService = new Mock<BizService>(mockConfigFactory.Object) { CallBase = true };
            spyService.Setup(s => s.HandleSQL()).Verifiable();

            //when
            var ex = Assert.Throws<ArgumentException>(() => spyService.Object.Process());

            //then
            Assert.That(ex.Message, Is.EqualTo("Not support non-SQL"));
        }
    }
  }

.. index:: Testing, c#, dotnet,Nunit 
