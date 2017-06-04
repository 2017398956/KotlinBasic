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












