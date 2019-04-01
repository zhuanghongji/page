# Java 设计模式：简介

**什么是设计模式？**

设计模式，是软件开发过程中所面临问题的一般性解决方案，是众多开发人员在很长一段时间中的试验和错误中总结出来的最佳实践。

<img src="./res/002.jpg" width="120"/>

> 合理地使用设计模式可增强代码的可靠性和可重用性，同时也使得代码更容易被他人理解。

总的来说，设计模式可分为三大类：

* **创建型 (Creation Patterns)**：提供一种 “隐藏了具体创建细节” 的对象创建方式。
* **结构型 (Structual Patterns)**：聚焦于类和对象的组合。
* **行为型 (Behavioral Patterns)**：聚焦于对象之间的通信。

| 创建型 | 结构型 | 行为型
| - | - | -
| 工厂模式 (Factory Pattern) | 适配器模式 (Adapter Pattern) | 责任链模式 (Chain of Responsibility Pattern)
| 工厂模式 (Factory Pattern) | 桥接模式 (Bridge Pattern) | 命令模式 (Command Pattern)
| 抽象工厂模式 (Abstract Factory Pattern) | 过滤器模式 (Filter、Criteria Pattern) | 解释器模式 (Interpreter Pattern)
| 单例模式 (Singleton Pattern) | 组合模式 (Composite Pattern) | 迭代器模式 (Iterator Pattern)
| 建造者模式 (Builder Pattern) | 装饰器模式 (Decorator Pattern) | 中介者模式 (Mediator Pattern)
| 原型模式 (Prototype Pattern) | 外观模式 (Facade Pattern) | 备忘录模式 (Memento Pattern)
|  | 享元模式 (Flyweight Pattern) | 观察者模式 (Observer Pattern)
|  | 代理模式 (Proxy Pattern) | 状态模式 (State Pattern)
|  |  | 空对象模式 (Null Object Pattern)
|  |  | 策略模式 (Strategy Pattern)
|  |  | 模板模式 (Template Pattern)
|  |  | 访问者模式 (Visitor Pattern)

当年 GOF 四位作者提出的设计模式主要基于以下两个原则：
* 对接口编程，而不是对实现编程。
* 优先使用对象组合。而不是继承。

> 本系列接下来的文章主要是基于 Java 示例来阐述各个设计模式的基本思想和应用。


**什么是六大原则？**

我们提到设计模式的同时通常也会提到六大原则，这两者之间是有什么关系么？

<img src="./res/001.jpg" width="120"/>

哈哈，但我知道六大原则是指以下六个：

* **开闭原则 (Open Close Principle)**：对扩展开放，对修改关闭。
* **里氏代换原则 (Liskov Substitution Principle)**：任何基类可以出现的地方，子类一定可以出现。
* **依赖倒转原则 (Dependence Inversion Principle)**：针对接口编程，依赖于抽象而不依赖于具体。
* **接口隔离原则 (Interface Segregation Principle)**：使用多个隔离的接口，比使用单个接口要好。
* **最少知道原则 (Demeter Principle)**：一个实体应当尽量少地与其他实体之间发生相互作用，使得系统功能模块相对独立。
* **合成复用原则 (Composite Reuse Principle)**：尽量使用合成/聚合的方式，而不是使用继承。


**如何让你的大脑就范？**

<img src="./res/003.jpg" width="120"/>

> 这段内容截取自《Head First 设计模式》，感觉可以作为日常学习的一个行为准则。

* 慢一点，你理解的越多，需要记的就越少。
* 学习过程中如果能实际动手，动手后再做个笔记，将改善你的学习效果。
* 遇到问题不要直接跳过，尝试去解决，它可能是核心的一部分。
* 上床之前不要看别的书了，至少不要看其它有难度的东西。
* 学习中有一部分是在你合上书之后完成的，你的大脑需要时间来做一些处理。
* 如果你还没完全理解一个知识点就去学习其它的，那么你刚才学的一部分知识点就会被丢弃掉。
* 要喝水，多喝点，你的大脑越放松，学习能力就更强。
* 如果你想看懂或更牢地记住它，那就大声说出来，并尝试解释给别人听，这样你可能会有一些新的认识或疑惑。
* 如果发现自己开始浮光掠影的翻看，或者刚看的东西很快就忘记了，说明你应该休息一下了。
* 去设计一些东西，将学到的知识应用到项目中，以加深对该知识的理解，并获取实践经验。

