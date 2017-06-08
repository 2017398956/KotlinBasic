# Kotlin 从学习到 Android 第九章 可见性修饰符 #
类、对象、接口、构造函数、函数、属性和它们的 setter 都可以有可见性修饰符。( getter 总是与属性具有相同的可见性。)在 Kotlin 中有四个可见性修饰符: private , protected , internal 和 public。如果没有用修饰符修饰，默认是 public 。

下面将介绍可见性修饰符在修饰不同类型时的作用：

## 包 ##
函数、属性和类、对象和接口可以在“顶级”中声明，也就是说直接在包中:

	// file name: example.kt
	package foo
	
	fun baz() {}
	class Bar {}

- 如果不声明可见性修饰符，默认是 public ，那么你所声明的可以被任意引用；
- 如果声明为 private, 则只能在这个文件中被引用；
- 如果声明为 internal, 则在同一个 module 可见；
- 顶级声明中不能使用 protected ；

## 类和接口 ##
对于类中的成员：

- private   ：只在该类中可见；
- protected ：只在该类和其子类中可见；
- internal  ：在同一个 module 中可见；
- public    ：任何位置都可见；

**java 用户注意**：在 Kotlin 中外部类不能使用内部类的私有成员。

如果你覆写了一个 protected 的成员，但是没有用可见性修饰符修饰，那么这个覆写的成员也是 protected 的。

## 构造函数 ##
要指定一个类的主构造函数的可见性，请使用以下语法(注意，需要添加 constructor 关键字):

	class C private constructor(a: Int) { ... }

这里构造函数是 private 的。默认情况下，所有的构造函数都是 public 的，这意味着它们在类可见的任何地方都可见(例如，一个内部类的构造函数只能在同一个 module 中可见)。

## 局部声明 ##
局部变量、函数和类不能具有可见性修饰符。

## module ##
internal 表示成员在同一个 module 中是可见的。更具体地说，一个 module 是一组汇集在一起的 Kotlin 文件:

- 一个 IntelliJ IDEA module;
- 一个 Maven 或者 Gradle 项目;
- 一组用 Ant 任务调用编译的文件；