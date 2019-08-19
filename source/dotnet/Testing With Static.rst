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




