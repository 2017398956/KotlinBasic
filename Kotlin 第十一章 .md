# Kotlin 从学习到 Android 第十一章 枚举类、嵌套类 和 密封类 #
## 一、枚举类 ##
枚举类最基本的用法是实现类型安全的枚举：

	enum class Direction {
	    NORTH, SOUTH, WEST, EAST
	}

每一个枚举常量都是一个对象，枚举常量间以 "," 分割。

### 初始化 ###
因为每个 enum 都是 enum 类的一个实例，所以可以对它们进行初始化：

	enum class Color(val rgb: Int) {
	        RED(0xFF0000),
	        GREEN(0x00FF00),
	        BLUE(0x0000FF)
	}

### 匿名类 ###
枚举常量还可以声明它们自己的匿名类：

	enum class ProtocolState {
	    WAITING {
	        override fun signal() = TALKING
	    },
	
	    TALKING {
	        override fun signal() = WAITING
	    };// 枚举类中定义了成员（下面的 signal() 函数），所以要用分号分开
	
	    abstract fun signal(): ProtocolState
	}

枚举常量的匿名类不仅可以定义自己的函数，也可以覆写枚举类的函数。**注意**：如果枚举类定义了任何成员，你必须用分号来分隔枚举常量和成员。
### 枚举常量的使用 ###
和 Java 类似，Kotlin 中的枚举类也有列出已定义的枚举常量方法和通过枚举常量的名称获取一个枚举常量的方法。例如(假定枚举类的名称为 EnumClass):

	EnumClass.valueOf(value: String): EnumClass
	EnumClass.values(): Array<EnumClass>

如果枚举常量匹配不到 valueOf() 方法参数的值，那么将会抛出一个 IllegalArgumentException 。

从 Kotlin 1.1 开始，可以使用 enumValues<T>() 和 enumValueOf<T>() 函数通过泛型的方式来访问枚举类中的枚举常量：

	enum class RGB { RED, GREEN, BLUE }
	
	inline fun <reified T : Enum<T>> printAllValues() {
	    print(enumValues<T>().joinToString { it.name })
	}
	
	printAllValues<RGB>() // RED, GREEN, BLUE

每一个枚举常量都有 name 和 ordinal 属性，分别标示这个常量在枚举类中的名称和次序：

	println(RGB.BLUE.name)			// BLUE
    println(RGB.BLUE.ordinal)		// 2

枚举常量也实现了 Comparable 接口，它们的自然顺序就是在枚举类中定义的顺序。

----------

## 二、嵌套类 ##
类中可以嵌套其它的类：

	class Outer {
	    private val bar: Int = 1
	    class Nested {
	        fun foo() = 2
	    }
	}
	
	val demo = Outer.Nested().foo() // == 2

### 内部类 ###
一个被 inner 标记的内部类可以访问外部类的成员。内部类对外部类的对象有一个引用:

	class Outer {
	    private val bar: Int = 1
	    inner class Inner {
	        fun foo() = bar
	    }
	}
	
	val demo = Outer().Inner().foo() // == 1

### 匿名内部类 ###
匿名内部类是通过 [对象表达式](http://kotlinlang.org/docs/reference/object-declarations.html#object-expressions) 进行实例化的：

	window.addMouseListener(object: MouseAdapter() {
	    override fun mouseClicked(e: MouseEvent) {
	        // ...
	    }
	                                                                                                            
	    override fun mouseEntered(e: MouseEvent) {
	        // ...
	    }
	})

如果这个匿名对象是 java 接口的实例化，并且这个接口只有一个抽象方法，那么你可以直接用 **接口名lambda表达式** 的方式来实例化：

	val listener = ActionListener { println("clicked") }

## 三、密封类 ##
密封类用于表示受限制的类层次结构，即一个值可以从一个有限的集合中拥有一个类型，但是不能有任何其他类型。从某种意义上说，它们是枚举类的扩展:枚举类型的值集也受到限制，但每个枚举常量仅作为单个实例存在，而密封类的子类可以包含多个实例并包含状态。
要声明一个密封类，你可以在类名之前添加 sealed 修饰符。一个密封类可以有子类，但是所有的类都必须在密封类本身所在的文件中声明。(在 Kotlin 1.1 之前，规则更加严格:类必须嵌套在密封类的声明中)。

	sealed class Expr
	data class Const(val number: Double) : Expr()
	data class Sum(val e1: Expr, val e2: Expr) : Expr()
	object NotANumber : Expr()

上面的例子使用了 Kotlin 1.1 的另一个新特性: [数据类](http://blog.csdn.net/niuzhucedenglu/article/details/72812074) 扩展其他类的功能，包括密封类。

**注意**：那些继承了一个密封类的子类(间接继承)的类可以放在任何地方，不一定是在同一个文件中。

	// file01.kt
	fun main(args: Array<String>) {
	
	    fun eval(expr: Expr): Double = when(expr) {
	        is Const -> expr.number
	        is Sum -> eval(expr.e1) + eval(expr.e2)
	        is TestN -> 20e0
	        NotANumber -> Double.NaN
			// 由于覆盖了所有的可能性，所以不需要 else 分支
	    }
	
	    print(eval(TestA())) 				// 20.0
	}
	sealed class Expr
	data class Const(open val number: Double) : Expr()
	data class Sum(val e1: Expr, val e2: Expr) : Expr()
	object NotANumber : Expr()
	open class TestN : Expr()

	// file02.kt
	class TestA() : TestN()

使用密封类的关键好处是当你在使用一个 when表达式 时，如果可以验证语句覆盖了所有的情况，那么就不需要向语句中添加 else 子句。