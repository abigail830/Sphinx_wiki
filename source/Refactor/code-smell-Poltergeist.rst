Poltergeist｜捉弄人
====================================

  Refer: http://www.tusharma.in/smells/POL.html

  当创建的类在系统的操作中只具有有限的职责时，就会出现这个坏味道。它可以以许多不同的形式出现，但是它们的思想是相同的：在面向对象设计中没有重要的作用。此外，poltergeist类的生命周期很短，并且在使用时浪费资源。
  

Riel将此反模式出现在OO设计中的5种不同方式进行了分类
---------------------------------------------------

1. **Irrelevant classes【无关联类】:** 无关联类是指在设计中没有任何有意义行为的类，例如 setter/getter/print。
2. **Agent classes【代理类】:** 代理类即只负责从一个到另一个类传递消息。
3. **Out of scope classes【超范围类】:** 如果这个类只负责发送消息到其他类,但从来没有收到任何消息，那就是超范围类
4. **Operation classes【操作类】:** 操作类的特征是只由一个有意义的行为组成，并且生命周期短
5. **Object classes【对象类】:** 对象类应该是父类实例的子类，而不是独立的一个类。



关联的味道
----------------

Imperative Abstraction 必要的抽象
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  当一个操作变成一个类时，就会产生这种气味。（如上面说的第4种）

代码中如何判断：

* （DesigniteJava的做法）类中只有1个public方法，且行数 > 某threshold


Unnecessary Abstraction 不必要的抽象
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  当在软件设计中引入了实际上并不需要的抽象(因此可以避免)时，就会出现这种味道。

代码中如何判断：

* （DesigniteJava的做法）类方法为0且field数目 < 某threshold




