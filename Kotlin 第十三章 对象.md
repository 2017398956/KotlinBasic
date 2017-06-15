# Kotlin 从学习到 Android 第十三章 对象 #
## 对象表达式和声明 ##
有时，我们想创建这样一个对象：只对原有的类做稍微的修改，而不需要声明一个该类的子类。在 java 中可以使用匿名内部类实现，而在 kotlin 中可以使用 **对象表达式** 和 **对象声明** 来实现。
## 对象表达式 ##
创建继承某种类型的匿名类的对象:

	window.addMouseListener(object : MouseAdapter() {
	    override fun mouseClicked(e: MouseEvent) {
	        // ...
	    }
	
	    override fun mouseEntered(e: MouseEvent) {
	        // ...
	    }
	})

如果一个超类有一个构造函数，那么必须将适当的构造函数参数传递给它。多个超类可以在冒号后以逗号分隔:

	open class A(x: Int) {
	    public open val y: Int = x
	}
	
	interface B {...}
	
	val ab: A = object : A(1), B {
	    override val y = 15
	}

有时，我们可能只需要“一个对象”这种形式，它不需要任何超类，这时可以简单地这么声明：

	fun foo() {
	    val adHoc = object {
	        var x: Int = 0
	        var y: Int = 0
	    }
	    print(adHoc.x + adHoc.y)
	}

**注意**：匿名对象可以在局部和私有声明中作为类型使用。如果你把一个匿名对象作为一个 public 函数的返回值类型 或 public 属性的类型，那么实际上该函数或属性的类型是匿名对象的超类型，如果你没有为匿名类声明任何超类，那么该函数或属性的类型是 Any 。另外，在匿名对象中添加的成员是不能在匿名对象外访问的。

	class C {
	    // 私有函数，所以返回值类型是匿名对象的类型
	    private fun foo() = object {
	        val x: String = "x"
	    }
	
	    // 公共函数，返回值类型是 Any
	    fun publicFoo() = object {
	        val x: String = "x"
	    }
	
	    fun bar() {
	        val x1 = foo().x        // 可以这么使用
	        val x2 = publicFoo().x  // 错误: publicFoo() 的类型是 Any ，Any 中没有 'x' 属性
	    }
	}

和 Java 的匿名内部类类似，对象表达式中的代码可以从封闭的范围访问外部的变量。(不像 Java 的是：这些变量不需要声明为 final 。)

	fun countClicks(window: JComponent) {
	    var clickCount = 0
	    var enterCount = 0
	
	    window.addMouseListener(object : MouseAdapter() {
	        override fun mouseClicked(e: MouseEvent) {
	            clickCount++
	        }
	
	        override fun mouseEntered(e: MouseEvent) {
	            enterCount++
	        }
	    })
	    // ...
	}

## 对象声明 ##
单例模式是一种非常有用的设计模式，在 kotlin 中可以很容易地创建一个单例：

	object DataProviderManager {
	    fun registerDataProvider(provider: DataProvider) {
	        // ...
	    }
	
	    val allDataProviders: Collection<DataProvider>
	        get() = // ...
	}

这种以关键字 object 开头，后跟名称的形式叫做对象声明。就像变量声明一样，对象声明不是表达式，也不能在赋值语句的右侧使用。

引用这个对象时，我们可以直接使用它的名字:

	DataProviderManager.registerDataProvider(...)

当然，这样声明的对象也可以有超类：

	object DefaultListener : MouseAdapter() {
	    override fun mouseClicked(e: MouseEvent) {
	        // ...
	    }
	
	    override fun mouseEntered(e: MouseEvent) {
	        // ...
	    }
	}

**注意**:对象声明不能是局部的(也就是说，不能直接嵌套在函数中)，但是它们可以嵌套到其他的对象声明或非内部类中。

object declarations can't be local (i.e. be nested directly inside a function), but they can be nested into other object declarations or non-inner classes.

## 伴侣对象（companion object） ##
companion 有 伴侣 同伴 伙伴 的意思，这里将 companion object 翻译为伴侣对象，因为：

![](/images/13_01.png)

一个类中只能有一个 companion object ，比较符合一夫一妻制，多夫多妻风俗的请绕道，谢谢。
一个类可以使用自己的名字作为限定符，调用伴侣对象的成员：

	val instance = MyClass.create()

伴侣对象的名称可以省略，在这种情况下，可以使用 Companion :

	class MyClass {
	    companion object {
	    }
	}
	
	val x = MyClass.Companion

**注意**:尽管伴侣对象的成员看起来像其他语言中的静态成员，但在运行时这些对象仍然是真实对象的实例成员，并且可以，例如，实现接口:

	interface Factory<T> {
	    fun create(): T
	}
	
	
	class MyClass {
	    companion object : Factory<MyClass> {
	        override fun create(): MyClass = MyClass()
	    }
	}

然而，如果你使用了 @JvmStatic 注解，那么你将在 JVM 上拥有作为静态方法和字段的伴侣对象成员，有关更多详细信息参见 [Java interoperability](http://kotlinlang.org/docs/reference/java-to-kotlin-interop.html#static-fields) 。

## 对象表达式和对象声明的语义差别 ##
在对象表达式和对象声明之间有一个重要的语义区别:

- 对象表达式在被使用时立即执行(并初始化)；
- 对象声明是在第一次访问时被初始化的；
- 在加载（解析）一个类时，其伴侣对象会被初始化，这一点符合 Java 中静态初始化器的语义。