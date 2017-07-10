# Kotlin 从学习到 Android 第十四章 委托 #

## 类的委托 ##

委托模式已经被证明是实现继承的一个很好的替代方案，而Kotlin支持它的本地要求零样板代码。派生的类可以从接口基础继承并将其所有公共方法委托给指定的对象:


The Delegation pattern has proven to be a good alternative to implementation inheritance, and Kotlin supports it natively requiring zero boilerplate code. A class Derived can inherit from an interface Base and delegate all of its public methods to a specified object:

	interface Base {
	    fun print()
	}
	
	class BaseImpl(val x: Int) : Base {
	    override fun print() { print(x) }
	}
	
	class Derived(b: Base) : Base by b
	
	fun main(args: Array<String>) {
	    val b = BaseImpl(10)
	    Derived(b).print() // prints 10
	}

派生的超级类型列表中的by子句表示，b将在派生的对象内部存储，而编译器将生成向前到b的所有基础方法。

The by-clause in the supertype list for Derived indicates that b will be stored internally in objects of Derived and the compiler will generate all the methods of Base that forward to b.

**注意**：重写工作是您所期望的:编译器将使用您的覆盖实现而不是委托对象中的那些实现。如果我们要添加重写fun print(){ print(“abc”)}来派生，程序将打印“abc”而不是“10”。

Note that overrides work as you might expect: The compiler will use your override implementations instead of those in the delegate object. If we were to add override fun print() { print("abc") } to Derived, the program would print "abc" instead of "10".