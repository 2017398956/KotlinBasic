# Kotlin 从学习到 Android 第六章 类和继承 #
## 类 ##
类在 Kotlin 中使用关键字 class 声明：

	class Invoice {
	}

类的声明包括类名，类头（参数类型，构造函数等）和类体（花括号包裹的内容）。类头和类体都是可选的；如果一个类没有类体，那么花括号可以省略。

	class Empty

## 构造函数 ##
在 Kotlin 中一个类可以有一个主要构造函数和多个次要构造函数。主要构造函数是类头的一部分，放在类名之后，并含有可选的类型参数。

	class Person constructor(firstName: String) {
	}

如果主构造函数没有任何注解或可见的修饰符，其关键字 constructor 可以省略不写。

	class Person(firstName: String) {
	}

主构造函数中不能含有代码语句，初始化代码可以放在以关键字 init 声明的 初始化块 中：

	class Customer(name: String) {
	    init {
	        logger.info("Customer initialized with value ${name}")
	    }
	}

注意：主构造函数中的参数可以用在 初始化块 中，也可用在类体中属性的初始化。

	class Customer(name: String) {
	    val customerKey = name.toUpperCase()
	}

一般我们可以在主构造函数中声明和初始化属性：

	class Person(val firstName: String, val lastName: String, var age: Int) {
	    // ...
	}

如果构造函数有注解或可见的修饰符，则关键字 constructor 不能省略且修饰符要放在 constructor 之前。
	
	class Customer public @Inject constructor(name: String) { ... }

## 次要构造函数 ##
类也可以用 constructor 来声明次要构造函数。

	class Person {
	    constructor(parent: Person) {
	        parent.children.add(this)
	    }
	}

如果一个类有主构造函数，那么每个次要构造函数都必须委托给主构造函数，或委托给另一个次要构造函数。在同一个类中，委托给一个构造函数使用 this 关键字。

	class Person(val name: String) {
	    constructor(name: String, parent: Person) : this(name) {
	        parent.children.add(this)
	    }
	}

如果一个非抽象的类没有任何构造函数，系统将为它自动生成一个无参的主构造函数，并且是 public 的。如果你不想这个类有一个 public 权限的构造函数，你需要声明一个无参构造函数，而不是使用系统默认的。

	class DontCreateMe private constructor () {
	}

**注意**：在 JVM 上，如果一个主构造函数的所有参数都有默认值，那么编译器将用这些值自动创建一个无参构造函数。这样一些第三方库如 JackJson 或 JPA 等就可以方便地通过无参构造函数进行实例化。

	fun main(args: Array<String>) {
	    var customer = Customer()
	    println(customer.customerName) // admin
	    // var student = Student() 
		// 由于 age 没有初始化，自动没有自动创建无参构造函数，编译器报错
	}
	
	class Customer(val customerName: String = "admin")
	
	class Student(val name: String = "HanMeimei" , val age: Int)

## 创建类的实例 ##
通常我们可以调用构造函数来创建一个类的实例：

	val invoice = Invoice()
	
	val customer = Customer("Joe Smith")

**注意**：Kotlin 中没哟关键字 new 。
## 类成员 ##
一个类中，通常包括下面几个部分：
- 构造函数和初始化块
- 函数
- 属性
- 嵌套类和内部类
- 对象声明

## 继承 ##
Kotlin 中所有的类都默认继承自 Any ，Any 是一个默认没有父类的超类。Any 不是 java.lang.Object；注意： Any 除了 equals(), hashCode() 和 toString() 没有其它任何函数。

我们可以在类头中通过冒号后跟父类来声明继承关系：

	open class Base(p: Int)
	
	class Derived(p: Int) : Base(p)

如果一个类有主构造函数，那么其父类在被继承时就必须被初始化，如上面代码。

如果一个类没有主构造函数，那么每个次要构造函数都需要用关键字 super 来初始化父类，或者委派给另一个已初始化父类的构造函数。另外，不同的次要构造函数可以调用父类中不同的构造函数。

	class MyView : View {
	    constructor(ctx: Context) : super(ctx)
	
	    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs)
	}

open 关键字和 java 中的 final 关键字功能相反：它允许其它的类继承自这个类。默认情况下 Kotlin 中所有的类都是 final 的。
## 覆写函数 ##
不像 java 那样，在 Kotlin 中覆写函数必须明确声明：

	open class Base {
	    open fun v() {}
	    fun nv() {}
	}
	class Derived() : Base() {
	    override fun v() {}
	    // fun nv(){} // 不用关键字 override 声明，编辑器会报错
		// override fun nv(){} // 父类中函数不声明为 open ，编译器也报错
	}

一个覆写函数默认是 open 的，它可以被子类覆写，如果你不想让它被子类覆写可以使用 final 修饰：

	open class AnotherDerived() : Base() {
	    final override fun v() {}
	}

## 覆写属性 ##
覆写属性和覆写函数的方式类似，如果子类中如果和父类中有同名的属性，那么子类中的属性必须用 override 修饰，且属性类型可兼容。

	open class Foo {
	    open val x: Int get { ... }
	}
	
	class Bar1 : Foo() {
	    override val x: Int = ...
	}

可以向上面那样通过初始化来覆写，也可用使用 getter 函数来覆写：

	open class A{
	    open val x:Int = 0
	}
	
	class B : A(){
	    override val x: Int
	        get() = TODO("not implemented")
	}

如果覆写时是 var 类型，且没有初始化，那么 getter 、 setter 都必须要有：

	open class A{
	    open var x:Int = 0
	}
	
	class B : A(){
	    override var x: Int
	        get() = TODO("not implemented")
	        set(value){...}
	}

你也可以用一个 var 类型的属性覆写一个 val 类型的属性，但是反过来不行。因为一个 val 类型的属性本质上是声明了一个 getter 函数，而用 var 对其覆写只是在子类中额外添加了一个 setter 函数。另外，你还可以再主构造函数中进行属性的覆写：

	interface Foo {
	    val count: Int
	}
	
	class Bar1(override val count: Int) : Foo
	
	class Bar2 : Foo {
	    override var count: Int = 0
	}

**注意**： 接口和接口中的成员默认都是 open 的。
## 覆写规则 ##
如果一个类和它的直接父类拥有同名的成员，那么这些成员必须被覆写；另外，在子类中我们可以使用 super<Base> 来确定调用了哪个父类（父接口）的哪个成员。

	open class A {
	    open fun f() { print("A") }
	    fun a() { print("a") }
	}
	
	interface B {
	    fun f() { print("B") } 
	    fun b() { print("b") }
	}
	
	class C() : A(), B {
	    override fun f() {
	        super<A>.f() // A.f()
	        super<B>.f() // B.f()
	    }
	}

## 抽象类 ##
一个类可以用 abstract 声明为抽象类。抽象类中的成员不需要在它自身中实现，另外，我们不需要为抽象类和它的成员声明 open ，它也是可以被继承的。

我们也可以用一个抽象类的抽象函数覆写一个非抽象类的非抽象函数：

	open class Base {
	    open fun f() {}
	}
	
	abstract class Derived : Base() {
	    override abstract fun f()
	}

## companion 对象 ##
不像 java 或 C# ，在 Kotlin 中，类没有静态方法。大多数情况，可以使用包级别的函数来代替。如果你需要声明一个不通过类实例化就能调用的函数并且可以访问这个类的内部结构，那么你可以在这个类中声明一个 object 。如果你在类中声明了一个 companion 对象，那么你就能像在 Java/C# 中调用静态方法那样调用这个对象的函数。