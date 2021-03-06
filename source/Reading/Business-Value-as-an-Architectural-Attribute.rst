为什么要从业务价值导向架构决策？
===================================

  Business Value as an Architectural Attribute
  
  Reference: https://martinfowler.com/articles/value-architectural-attribute.html


使用价值流映射检查架构
--------------------------------

评估架构的业务价值，第一步是要对IT环境中的各种系统和组件进行价值流映射。业务人员常常会对业务流程进行价值流映射，分析用户旅程各个部分如何影响收入和利润，而一旦达到目的，映射分析就会停止，大家也不会尝试从架构角度来映射价值流。

架构师应当把这种映射继续拓展到架构层面，将业务指标也对应地分配给支撑业务流程的系统。除了财务指标外，重要的非财务指标也可以一样的映射。例如‘客户线上查询索赔状态’的功能在多大程度上影响客户留存?(这类指标通常很难制定，但在思考过程中往往可以得出重要的洞见。)

最近在一个客户那里我们就进行了这样一个练习，从客户与公司交互的用户旅程开始，把步骤都贴在团队办公室的墙上，再将旅程的每个步骤与客户的系统及组件联系起来。接着，就可以开始评估每个系统是怎样对用户旅程中的各个步骤做出贡献，以及系统失败可能带来的业务影响。


充分考虑系统失败对业务价值带来的影响
------------------------------------

正如上面例子提到的，评估系统失败可能带来的业务影响是一个特别重要的点。例如，当纠结是否要提高一个系统的弹性时，最好的判断方法是看系统失败时带来的业务风险。曾经遇到一家零售商，他们希望去验证备份数据库的恢复过程是正确的。然而圣诞节前还有大量其他的业务功能特性在排队，因此大家很难对数据库备份及修复这样的技术任务进行优先级排序。我们尝试询问，如果在“黑色星期五”发生数据库崩溃的话，损失成本是多少，希望系统多快恢复? 这让业务人员马上决定去主导这个围绕数据库恢复进行的彩排并缩短恢复时间，若不是这样询问，这项工作估计将继续淹没在IT的需求队列中。

评估风险价值有两种不错的方式。一种是自上而下，查看业务功能，并确定哪些软件系统支撑该功能。另一种方法则相反，从软件系统开始，考虑系统失败会产生什么样的后果。

分析风险价值时，最重要的，是要认识到不同系统失败带来的后果及其严重性是不一样的，正如并不是所有的系统组件都需要相同的弹性级别。假设，在Boxing day的时候(相当于英国的黑色星期五)股票管理系统出现了故障。如果这会导致无法查看库存数据，那我们要么选择接受订单，即使不确认是否能完成，要么就拒绝订单，但两种选择都会造成重大损失。而如果是在发货系统中出现故障，这导致订单在队列中等待处理，并最终延迟交付。如果业务认为后一种情况不太值得关注，那大家就要准备好面对这种架构较弱弹性带来的风险。不管具体场景是什么，弹性的程度应该是一个商业决策。数据一致性也是如此。在设计分布式数据库时，架构师通常不愿放松可用性的一致。但是，重复预订几间酒店房间的商业成本可能比根本不接受预订要少得多。在一致性和可用性之间进行权衡是一个商业决策，而不是一个技术决策。这也体现在CAP定理中（译者注 CAP: https://zh.wikipedia.org/wiki/CAP%E5%AE%9A%E7%90%86)。


跨功能需求（CFRs) 也应业务价值导向
---------------------------------------

这里涉及一个宗旨，不应该只是评估业务功能的带来的价值，还应该评估系统特性的价值，系统特性常被归类为跨功能需求(即非功能性需求，如“性能”)。如果想让系统达到某些技术指标，那么就需要让非技术人员也了解到，如果不这样做将会失去什么业务价值。评估跨功能需求（CFRs)的业务价值通常是困难的，许多技术人员回避由此产生的争论。但如果做不到正确地评估，造成的危害将不仅仅是对低价值技术的过度投资。也将给技术人员和用户之间的合作设置了一个真正的障碍。

理解价值将有助于做出对于组件灵活性需求的决策。举个例子，某客户有一个处理支付的组件，该组件需要与某支付提供商对接。客户希望这组件易于配置，以便在支付提供商发生变化时能够快速调整。这时，通常存在两种的选择。一是在该组件中硬编码所有对接时需要的参数，而另一个则是让所有参数都是可配置的。借助可配置选项，组件可以在几天内通过更改配置文件的方式，配合支付提供商的变动。然而，通常这种可配置性会增加组件代码的复杂性，从而增加其他更改的成本。这是与可配置性之间的一种常见的权衡：当然，也可以选择就允许其他地方更改更困难，而坚持简化可配置项的更改。这里至关重要的是要考虑到，在与支付提供商交互的过程中最困难的部分其实是什么？如果难点在于协商一份新的法律合同，这通常需要一年多的时间。那上面简化配置支付提供者信息就是不值得的，因为即使修改一个不可配置的组件更花时间，但仍然比法律协商要快得多。


业务价值导向意味着跨功能需求（CFRs)因组件而异
-------------------------------------------

上述两个例子都说明，对弹性和灵活性等问题采用“一刀切”的方法毫无用处，更是一种浪费。几年前，我们曾与一家机构合作，该机构决定实施“5个9”的可用性要求，令人惊讶的是，这一规定甚至被应用于一个仅供内部员工使用的订餐系统。架构提供不同层次的服务，并与业务达成一致，这是非常有用的，例如，服务的损失会不会立即影响客户体验或收入，或者我们是否能够承受在数据库恢复过程中，服务被中断几个小时?

在分析系统如何支撑业务价值过程中，还有一要点，单个组件必须支持多个不同的价值流和对应的可靠性级别。在需要同时支持多个不同业务流程的单体架构中，这很常见，也是将事情分解的一个重要动机，例如，仅在某相应的业务流中，允许为达到高可用性而额外投入


使用监控来评估业务价值
-----------------------------------------

丰富的监控能让我们更好地了解系统的行为方式，这对于日益复杂的分布式系统来说尤其重要。这种监控通常聚焦系统健康状况，支撑生产环境的QA（https://martinfowler.com/articles/qa-in-production.html）。其实，监控也可以用来评估系统对业务价值的贡献，例如确定某特定的组件带来了多少销售收入。一个零售客户发现，监控大型机上的队列长度和消息速率可能是一个很好的间接指标，它能从侧面度量出每个商店的收入, 虽然向业务人员提供这种数据并不准确，但是至少可以让他们发现，单纯从技术角度进行监控，将可能遗漏重要的业务信息。另一个客户把他们网站上每一笔交易的收入都精确地计算出来，并在办公室的屏幕上实时地可视化。随着时间的推移，这比那些只显示CPU、内存等技术指标的监控更受关注。

有人可能会说，收集这些数据并不是理解系统行为的一部分，但我们相信，这对于理解软件组件对业务的贡献是至关重要的。随着越来越多的系统托管在云上，每个FaaS功能都能提供独立的账单。既然可以获取这个粒度的成本，那我们也应该努力获取对应粒度的收益数据。

定期的监测数据可以独立客观地为投资决策提供信息。曾遇到这么一个案例，某政府机构为公民提供网上服务。如果要同时支持旧浏览器，添加新功能的成本将显著增加，但为了照顾到那些无法升级的公民，进行兼容性的操作似乎又是迫于无奈。后来通过分析网站的流量，发现其实已经很少有市民使用这么老的浏览器了，与其支持旧的浏览器，还不如给还没升级的公民送一台新电脑和一束花来的更便宜。


如果要上云，只搬有价值的部分
-----------------------------------------

随着云系统的兴起，许多组织希望将软件系统迁移到云上。与以往多年的遗留系统迁移类似，公司希望系统在能支持相同功能的同时，能迁移到更现代(希望也是更高效)基础设施上。在几十年的职业生涯中，我们看到了不少类似的努力，也看到了一些很容易犯的错误。其中最常见的一种观点是，进行系统迁移的最简单方法是功能特性等价替换，在新平台上精确地重建现有的功能。这观点忽略了一个事实，现有系统里，有些功能几乎是没有价值的，有些功能根本没有人使用，有些功能甚至还干扰了业务流程的优化提升。功能特性等价替换比人们通常认为的要难得多，花时间分析并避免复制很少使用的功能，反而能让迁移更容易。

某合作过的公司最近启动了一项迁移工作，目标是将他们所有的物流处理代码迁移到一个新的系统中，这花费了好几个月的时间，并动用了许多开发人员。当我们与企业谈论未来规划时，他们解释说，由于成本高，他们计划取消对多种包装的支持。在迁移过程中，花费最多时间的就是打包功能相关的各种边界场景处理，但其实即使没有了这个功能，业务也完全没问题。

可见，更好地理解业务价值，对重新构建平台的规划将提供非常大的帮助。如果现有组件并没有贡献太多业务价值，那就不应该将它们复制到新平台中。通常来说，一个公司的大部分服务总会遵循一些常见的套路，但是总有一些罕见的场景。而这些罕见的场景通常又需要特殊的软件支持，那么，在决定是否需要在新平台上重新支持这些边缘场景之前，应该对该场景进行重新评估。如公司准备在未来六个月内停止提供这项服务，那在重新实施规划里取消相关的迁移也是可以理解的。


业务价值至关重要，但变化无常
--------------------------------

与生活或软件架构中的任何东西一样，价值评估不是一成不变的。曾与一家保险公司合作，该公司凭借其评级模型，在市场上拥有竞争优势。这个评级软件被公司视为掌上明珠。随着时间的推移，在网上直接进行保险报价的方式有了很大的转变。评级软件所需的很多参数，在代理与客户前期见面阶段就可以合理地获取，如果还把这复杂的表单保留在网页上进行输入，这软件就太没有吸引力了。随着这一转变，原有评级软件价值也逐渐消失了。因此，除了要评估软件资产的当前价值，还应该尝试对软件资产的价值如何受技术和业务环境变化的影响进行粗略的预测。

另一个零售商类似的例子，他们的目录管理系统可以轻松应对一年两次的更新，却无法应对快速的在线变化。尽管收入损失的机会成本从来都不容易量化，但当需要做重组或重写组件的投资决策时，就必须考虑到这点。


了解业务知识应该是技术人员职业发展的重要一环
---------------------------------------------

当人们看到技术领导者的成长时，大多数注意力都集中在“硬”技能上。各种软件平台的培训课程比比皆是，各种认证也是如此。在技术技能开发方面，其实我们更提倡将培训重点放在核心原则上，而不是当今流行的平台。“软”技能其实更难掌握，随着职位上升，这部分能力也更重要，这点也是我们所认可的。[1]

对技术领导者来说，硬技能很有价值，但对所从事的业务有深入的理解也同等重要，还必须了解到这个领域中各个参与者是如何创造价值的。这不是通过培训课程就可以获得的，需要与其他商业领袖定期互动。这种社交互动应该在职业生涯的早期就开始。将IT员工与业务人员分开的想法，会给诸如软件开发这样的行业带来无数弊病，软件与它所支撑的企业活动紧密相关, 软件本身的价值也正源于此。开发人员应当尽早地了解，如何与用户和客户保持频繁接触，并把这件事情做好。以后，当他们成为领导者了，并与一起成长的业务伙伴打成一片，这多年的交情将会给他们带来巨大的回报。

在漫长的软件开发生涯中，业务和IT之间的沟通障碍，一直让我们觉得悲哀。当架构师脱离了对业务价值流的理解时，会增加技术工作的浪费，也可能会错失环境变化所带来的机遇。技术领导者需要更多地关注业务和系统决策之间的相互作用，这也应该是所有技术人员职业发展的重要一环。


Footnotes
-------------
1. 这些被称为“软”技能，因为它们比“硬”技能更难。
















