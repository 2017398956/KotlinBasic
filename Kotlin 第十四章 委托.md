# Kotlin 从学习到 Android 第十四章 委托 #
## 类的委托 ##
委托模式已经被证明是实现继承的一个很好的替代方案，而Kotlin支持它的本地要求零样板代码。派生的类可以从接口基础继承并将其所有公共方法委托给指定的对象:


The Delegation pattern has proven to be a good alternative to implementation inheritance, and Kotlin supports it natively requiring zero boilerplate code. A class Derived can inherit from an interface Base and delegate all of its public methods to a specified object: