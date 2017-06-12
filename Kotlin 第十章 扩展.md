# Kotlin 从学习到 Android 第十章 扩展 #
与 C# 、Gosu 类似，Kotlin 也可以为一个类扩展一个新的功能，而不需要从类继承或使用任何类型的设计模式，如装饰（ Decorator ）。这是通过一种被称为**扩展**的特殊声明完成的。Kotlin 支持扩展函数和扩展属性。

## 扩展函数 ##
声明扩展函数时，我们需要在函数名前加上一个接收类型，也就是被扩展的类型。例如：下面的代码为 MutableList<Int> 添加一个 swap 的新函数。

	fun MutableList<Int>.swap(index1: Int, index2: Int) {
	    val tmp = this[index1] 
	    this[index1] = this[index2]
	    this[index2] = tmp
	}

上面代码中的 this 代表的是 MutableList<Int> 。扩展了 swap 函数后，MutableList<Int> 类型的数据就可以直接使用 swap 函数了：

	val l = mutableListOf(1, 2, 3)
	l.swap(0, 2)

当然在扩展函数中我们也可以使用泛型：

	fun <T> MutableList<T>.swap(index1: Int, index2: Int) {
	    val tmp = this[index1] // 'this' corresponds to the list
	    this[index1] = this[index2]
	    this[index2] = tmp
	}

对于不熟悉 Kotlin 的童鞋，上面的代码可能看不明白，下面我们为自己的类来扩展一个函数用以演示 Kotlin 的扩展功能：

	fun main(args: Array<String>) {
	    var ec = ExtensionedClass()
	    ec.nameSetter("admin")
	    ec.alertName()			// 这里可以直接调用扩展函数
	    println(ec.name)		// hello admin , 可见扩展函数调用成功
	}
	
	// 为 ExtensionedClass 添加一个新的函数
	fun ExtensionedClass.alertName(){
	    this.name = "hello " + this.name
	}
	
	class ExtensionedClass{
	
	    var name: String? = null
	
	    fun nameSetter(str: String){
	        name = str
	    }
	}

## 扩展解析静态 ##
扩展实际上并不修改它们扩展的类。通过定义一个扩展，你不需要将新成员插入到一个类中，而仅仅是使用这个类型的 **变量.扩展函数** 来调用。

我们想要强调的是，扩展函数是静态分派的，也就是说，它们不是由接收者类型来实现的。这意味着被调用的扩展函数是由调用函数的表达式的类型决定的，而不是在运行时对表达式求值的结果。例如：

	open class C
	
	class D: C()
	
	fun C.foo() = "c"
	
	fun D.foo() = "d"
	
	fun printFoo(c: C) {
	    println(c.foo())
	}
	
	printFoo(D())			// c

打印结果为 c ，因为调用的扩展函数只依赖于已声明的参数 C 类型，即 C 类。

如果一个类有一个成员函数，并且一个扩展函数被定义为具有相同的接收者类型，相同的名称，并且适用于给定的参数，那么在调用时总会调用成员函数。例如:

	class C {
	    fun foo() { println("member") }
	}
	
	fun C.foo() { println("extension") }

如果我们调用 c.foo() ，将会打印 "member", 而不是 "extension"。

但是，对于扩展函数来说，重载成员函数是完全可以的，这些函数具有相同的名称但是不同的签名:

	class C {
	    fun foo() { println("member") }
	}
	
	fun C.foo(i: Int) { println("extension") }

如果调用 C().foo(1) 将会打印 "extension"。

## 可 null 接收器 ##
注意，可以用可空的接收方类型定义扩展。这样的扩展可以在对象变量上调用，即使它的值为 null ，并且可以在函数体中检查 this == null ，这将允许你在 Kotlin 中调用 toString() 函数而不用检查是否为 null ： 因为在扩展函数中检测了。

	fun Any?.toString(): String {
	    if (this == null) return "null"
	    // 检测完是否是 null 后，下面的 this 会自动转化为非 null 类型
	    return toString()
	}

## 扩展属性 ##
扩展属性和扩展函数类似：

	val <T> List<T>.lastIndex: Int
	    get() = size - 1

注意，由于扩展实际上并没有将成员插入到类中，所以对于扩展属性来说没有有效的方法来拥有一个支持字段（backing field）。这就是为什么初始化器不允许扩展属性的原因。它们的行为只能通过显式地提供getter/setter来定义。例如：

	val Foo.bar = 1 // 错误: 扩展属性不能被初始化

## Companion 对象扩展 ##
如果一个类有 [companion 对象](http://kotlinlang.org/docs/reference/object-declarations.html#companion-objects)，那么你也可以为这个 companion 对象定义扩展函数和扩展属性：

	class MyClass {
	    companion object { }  // will be called "Companion"
	}
	
	fun MyClass.Companion.foo() {
	    // ...
	}


就像 companion 对象的常规成员一样，可以只使用类名作为限定符来调用它们:

	MyClass.foo()

## 扩展的作用范围 ##
大多数时候，我们在顶层定义扩展，也就是直接在包下面：

	package foo.bar
	 
	fun Baz.goo() { ... } 

要在其声明包之外使用这种扩展，我们需要在调用点上导入它

	package com.example.usage
	
	import foo.bar.goo // 只引入 goo
					   // 或者
	import foo.bar.*   // 全部引入
	
	fun usage(baz: Baz) {
	    baz.goo()
	}

## 声明扩展成员 ##
在一个类中，您可以为另一个类声明扩展。在这样的扩展中，有多个隐式接收器-对象成员可以在没有限定符的情况下被访问。扩展被声明的类的实例称为分派接收器，而扩展方法的接收者类型的实例称为扩展接收器。

	class D {
	    fun bar() { ... }
	}
	
	class C {
	    fun baz() { ... }
	
	    fun D.foo() {
	        bar()   // calls D.bar
	        baz()   // calls C.baz
	    }
	
	    fun caller(d: D) {
	        d.foo()   // call the extension function
	    }
	}

如果在分派接收器和扩展接收器之间发生名称冲突，则扩展接收器优先。要引用调度接收器的成员，你可以使用[限定语法](http://kotlinlang.org/docs/reference/this-expressions.html#qualified)。

	class C {
	    fun D.foo() {
	        toString()         // D.toString()
	        this@C.toString()  // C.toString()
	    }

声明为成员的扩展可以在子类中声明为 open 和 overridden  。这意味着分派这些函数对于分派接收器类型是虚拟的，但是对于扩展接收器类型来说是静态的。

	open class D {
	}
	
	class D1 : D() {
	}
	
	open class C {
	    open fun D.foo() {
	        println("D.foo in C")
	    }
	
	    open fun D1.foo() {
	        println("D1.foo in C")
	    }
	
	    fun caller(d: D) {
	        d.foo()   // 调用扩展函数
	    }
	}
	
	class C1 : C() {
	    override fun D.foo() {
	        println("D.foo in C1")
	    }
	
	    override fun D1.foo() {
	        println("D1.foo in C1")
	    }
	}
	
	C().caller(D())   // "D.foo in C"
	C1().caller(D())  // "D.foo in C1" - dispatch receiver is resolved virtually
	C().caller(D1())  // "D.foo in C" - extension receiver is resolved statically

## 使用扩展功能的动机 ##
在 Java 中， 我们通常命名一些工具类，如：FileUtils, StringUtils 等等。著名的类 java.util.Collections 也是属于这种风格。但是当使用这些工具类时，会让我们感觉不舒服，例如： 

	// Java
	Collections.swap(list, Collections.binarySearch(list, Collections.max(otherList)), Collections.max(list))

当然，为了简化代码，我们可以通过静态导入达到下面的效果：

	// Java
	swap(list, binarySearch(list, max(otherList)), max(list))

这稍微好一点，但是 IDE 强大的代码补全功能对我们的帮助还是太少。如果能像下面这样就好了：

	// Java
	list.swap(list.binarySearch(otherList.max()), list.max())

但是，我们又不想在类中实现完所有有可能用到的方法，所以，这就是扩展功能的优势所在。