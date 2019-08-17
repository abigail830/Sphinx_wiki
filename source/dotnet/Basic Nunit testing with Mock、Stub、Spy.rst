Mock、Stub、Spy of Nunit
===================================

* `Background`_
* `测试1: 使用真的Purchase, Mock的Printer`_
* `测试2: 使用Stub的Purchase, Mock的Printer`_
* `测试3: 使用Spy的Purchase, Mock的Printer`_
* `Quick Note`_

Background
------------------

假设生产代码如下，收银机CashRegister接受购买详情Purchase再进行打印Printer。 其中Printer并未完成开发

Purchase.cs

.. code-block:: C#
  
  using System;
  using AnotherMvc.infrastructure;
  using AnotherMvc.Service.infrastructure;

  namespace AnotherMvc.Service.mock
  {
    public class CashRegister
    {
        private IPrinter printer;
        public CashRegister(IPrinter printer)
        {
            this.printer = printer;
        }

        public void process(IPurchase purchase)
        {
            printer.Print(purchase.GetItems());
            printer.Print(purchase.getDefaultItem());
        }

    }
  }

Printer.cs

.. code-block:: C#
  
  using System;
  using AnotherMvc.infrastructure;

  namespace AnotherMvc.Service.infrastructure
  {
    public class Printer : IPrinter
    {
        public Printer()
        {
        }

        public void Print(string items)
        {
            throw new System.NotImplementedException("Method not implement yet");
        }
    }
  }
  
Purchase.cs
  
.. code-block:: C#
    
  using System;
  namespace AnotherMvc.Service.infrastructure
  {
    public class Purchase : IPurchase
    {
        string items;
        public Purchase(string item)
        {
            this.items = item;
        }

        public string getDefaultItem()
        {
            return "Default item: N/A.";
        }

        public string GetItems()
        {
            return this.items;
        }
    }
  }


测试1: 使用真的Purchase, Mock的Printer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: C#
  
  using NUnit.Framework;
  using System;
  using Moq;
  using AnotherMvc.Service;
  using AnotherMvc.infrastructure;
  using AnotherMvc.Service.mock;
  using AnotherMvc.Service.infrastructure;

  namespace AnotherMvc.test.Service
  {
    [TestFixture()]
    public class CashRegisterTest
    {
        [Test(Description = "Mock the printer & with real purchase")]
        public void TestCase_should_able_to_trigger_print_with_items_content()
        {
            //given
            Mock<IPrinter> mockPrinter = new Mock<IPrinter>();
            mockPrinter.Setup(p => p.Print(It.IsAny<string>())).Verifiable();

            //这里使用真数据
            var purchase = new Purchase("This is purchase");

            //when
            var cashReg = new CashRegister(mockPrinter.Object);
            cashReg.process(purchase);

            //then
            mockPrinter.Verify(p => p.Print("This is purchase"), Times.Once);
            mockPrinter.Verify(p => p.Print("Default item: N/A."), Times.Once);
        }
    }
  }
  
测试2: 使用Stub的Purchase, Mock的Printer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: C#
  
  using NUnit.Framework;
  using System;
  using Moq;
  using AnotherMvc.Service;
  using AnotherMvc.infrastructure;
  using AnotherMvc.Service.mock;
  using AnotherMvc.Service.infrastructure;

  namespace AnotherMvc.test.Service
  {
    [TestFixture()]
    public class CashRegisterTest
    {
        [Test(Description = "Mock the printer & with stub purchase")]
        public void TestCase_should_able_to_trigger_print_with_items_content_stub_purchase()
        {
            //given
            Mock<IPrinter> mockPrinter = new Mock<IPrinter>();
            mockPrinter.Setup(p => p.Print(It.IsAny<string>())).Verifiable();

            //stub用于给when准备外部假数据，后续不需要verify
            Mock<IPurchase> stubPurchase = new Mock<IPurchase>();
            stubPurchase.Setup(pur => pur.GetItems()).Returns("This is the mock return");
            stubPurchase.Setup(pur => pur.getDefaultItem()).Returns("This is the mock default");

            //when
            var cashReg = new CashRegister(mockPrinter.Object);
            cashReg.process(stubPurchase.Object);

            //then
            mockPrinter.Verify(p => p.Print("This is the mock return"), Times.Once);
            mockPrinter.Verify(p => p.Print("This is the mock default"), Times.Once);
        }
    }
  }
  

测试3: 使用Spy的Purchase, Mock的Printer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: C#
  
  using NUnit.Framework;
  using System;
  using Moq;
  using AnotherMvc.Service;
  using AnotherMvc.infrastructure;
  using AnotherMvc.Service.mock;
  using AnotherMvc.Service.infrastructure;

  namespace AnotherMvc.test.Service
  {
    [TestFixture()]
    public class CashRegisterTest
    {
        [Test(Description = "Mock the printer & with spy purchase")]
        public void TestCase_should_able_to_trigger_print_with_items_content_spy_purchase()
        {
            //given
            Mock<IPrinter> mockPrinter = new Mock<IPrinter>();
            mockPrinter.Setup(p => p.Print(It.IsAny<string>())).Verifiable();

            //生成一个真的purchase,只override其中GetItems一个方法，其他方法是真的
            Mock<IPurchase> spyPurchase = new Mock<IPurchase>() { CallBase = true };
            spyPurchase.Setup(pur => pur.GetItems()).Returns("This is the mock return");

            //when
            var cashReg = new CashRegister(mockPrinter.Object);
            cashReg.process(spyPurchase.Object);

            //then
            mockPrinter.Verify(p => p.Print("This is the mock return"), Times.Once);
            mockPrinter.Verify(p => p.Print("Default item: N/A."), Times.Once);
        }
    }
  }

Quick Note
-----------------
* Mock: Mock<IPrinter> mockPrinter = new Mock<IPrinter>();
* Spy: Mock<IPurchase> spyPurchase = new Mock<IPurchase>() { CallBase = true };
* Stub: stubPurchase.Setup(pur => pur.GetItems()).Returns("This is the mock return");
* Verify: mockPrinter.Verify(p => p.Print("This is the mock return"), Times.Once);


.. index: Testing, dotnet, C#, Nunit
