You’re Not Actually Building Microservices
====================================================

.. note::
  **This is article from Justin Etheredge** `You’re Not Actually Building Microservices <https://www.simplethread.com/youre-not-actually-building-microservices/>`_

我最近读了一篇名为《`The False Dichotomy of Monoliths and Microservices <https://jimmybogard.com/the-false-dichotomy-of-monoliths-and-microservices/>`_ 》（错误的单体和微服务二分法）的文章，作者是Jimmy·Bogard，我非常喜欢这篇文章。在通读全文时，我注意到他提到了我使用过的一个妙语，即将大多数微服务实现称为“分布式单体”。我简单地想了想，也许是我创造了这个术语，但是谷歌很快让我明白了这个想法是荒谬的。我之所以说“荒谬”，是因为这个短语以前就已经使用过了，它非常完美地描述了大多数greenfield微服务实现的转变。

在我们进入细节之前，我必须坦白:我是单体架构的超级粉丝。近年来，由于微服务热潮，它受到了很多负面的报道。但我认为问题的真正根源在于，开发人员使用的应用程序在多年的时间里缓慢地积累了越来越多的功能(通常没有测试)，这些应用程序变得非常脆弱，很难更改。我不会否认，这是一个大问题!这是面向服务体系结构和现在的微服务(微服务是SOA的子集吗?)设计的核心问题之一。

我不是在建立微服务吗?
------------------------

在回答这个问题之前，让我们定义一下大多数人认为微服务的对立面是什么...单体。目前的挑战之一是为单体找到一个完整的定义，因为它们通常是在实现SOA或微服务的上下文中定义的。我认为维基百科的定义其实很好:

  如果一个软件系统由一个独立的单体结构组成，那么它就被称为“单体”，在这个体系结构中，功能上不同的方面(例如数据输入和输出、数据处理、错误处理和用户界面)都是相互交织的，而不是由架构中独立的组件/模块提供。

其基本思想是，事物不是被分解成独立的组件，而是相互交织在一起的。从上面Jimmy的文章中可以看出，在这个定义中并没有说到这些作品在物理上是否彼此分散。我们喜欢将单体看作一个大型的单一代码库，运行在单一系统上，但事实并非如此。一个单体的核心原则是系统功能是相互交织的。

如果我们遵循这个逻辑，那么一个分布式的单体是什么?它将是一组相互交织的物理分布式服务。听起来是不是很熟悉?希望对你有帮助，这不是太熟悉。

你看，分布式的单体是世界上最糟糕的。您已经获得了单个单体代码库的相对简单性，并且您已经用它来交换了一个深深地交织在一起的分布式系统。那么，你是在构建微服务吗?看看这些症状，自己决定:

* 对一个微服务的更改通常需要对其他微服务的更改
* 部署一个微服务需要同时部署其他微服务
* 你的微服务太唠叨了（译者注：如处理一件事情的时候互相调用很多）
* 相同的开发人员在大量微服务中工作
* 许多微服务共享一个数据存储
* 您的微服务共享许多相同的代码或模型

现在我并不是说如果您的微服务拥有这些症状之一就是有问题了，总是有例外的。但在大多数情况下，如果你对上面的几点点头，你很可能并不是在使用微服务。


如何使用微服务?
------------------

我非常喜欢用婴儿学步的方式进行。说到微服务，我的想法也没有什么不同。对于许多新开发的系统，我几乎总是建议从一个整体开始。单体很简单，你可以在逻辑上分离你的系统，在一个单独的代码库中。但是，许多系统有许多固有的紧密联系的概念，在您对问题领域有深入的了解之前，很难清楚地分离这些概念。有时你会意识到这些概念是永远无法分离的!

斯特凡·蒂尔科夫(Stefan Tilkov)发表了一篇颇有见地的文章，反对在你怀疑自己需要微服务时，从一块单体开始。但他仍然承认定义边界在Greenfield系统中的难度: “我同意你应该在清晰地知道业务领域边界之后再试图对架构进行划分, 尽管:在我看来,理想的情况下（划分微服务）是你在构建现有系统的第二个版本。”

即使对系统有一定的了解，设计服务边界也并不容易，而且出错的代价也很大。从一个单体开始可以让您找出系统中的天然边界位置，这样您就可以以一种明智的方式设计服务边界。

避免分布式的整体
------------------

我相信，人们在使用小型和中型的单体软件时遇到的许多问题可以通过一个良好的自动化测试套件和自动化部署来缓解。在开始使用新系统时，拥有一个单一的、非分布式的代码库是一个巨大的优势。它可以让你更容易思考代码,允许您更容易地测试您的代码,允许您快速地变化,而不必担心编排服务之间的分布式监测,保持你的服务同步,最终一致性,而在微服务架构中你将会碰到所有这些事情。这些事情乍一看似乎不是巨大的挑战，但实际上是需要克服的巨大障碍。

如果你从一个单体开始，你的目标应该是防止它变成一个庞然大物。这样做的关键是简单地监听您的应用程序!我知道，这说起来容易做起来难，但随着单体越来越大，要不断问自己几个问题:

* 有什么东西在以不同于系统其他部分的速度伸缩吗?
* 系统中是否有什么部分感觉是“附加到系统上的”?
* 有什么部分比系统的其他部分变化得更快的吗?
* 系统中是否有一部分比其他部分需要更频繁的部署?
* 系统中是否有一个单独的人或小团队独立操作的部分?
* 数据存储中是否有一个表子集没有连接到系统的其他部分?

随着系统规模和开发人员数量的增加，您将发现分离服务将变得非常普遍。你听说过像Netflix或Uber这样的公司，它们的生态系统中有数百种微服务。这些公司处于规模的极端。他们有成千上万的开发人员，通常每天部署成千上万次。他们需要大量的细粒度服务，以使其中任何一个变得合理和可管理。但是，他们可能认为的细粒度服务可能是小型公司的中型应用程序。有一点很重要而需要随时留意的，那就是要那些成功部署了大量微服务的系统的规模。

当你往这个极端显著地走下去时，你会开始发现，越来越倾向于拥有少量中等规模的单体，并被一个服务生态系统所包围，这才是更有意义的。当你走到另一个极端的时候，你会发现在整个系统中只有不到5个开发者在工作，坚持一个单一的系统并可能取消一两个服务是非常合理并有巨大优势的。

如果您从这篇文章中得到了什么，我希望微服务是有用的，但不应该总是选择它作为默认的架构。划分服务使一些事情更容易，而另一些事情更困难，所以根据您的系统和组织，希望你可以在开始之前考虑清楚。

总结一下，我把它留给Martin Fowler:“不要考虑微服务，除非你有一个过于复杂的系统，以至于无法作为一个单体来管理。”
