# Kotlin 从学习到 Android 第三章 编码规范 #
## 1.命名风格  ##
如果不确定某种类型的命名风格，可以使用 java 的命名风格。

- 名称使用驼峰命名法 (不要使用下划线)
- 类型以大写字母开头
- 方法和属性以小写字母开头
- 使用 4 个空格缩进
- 公共函数要有说明文档

## 2.冒号 ##
类型和超类间的冒号前应该有一个空格，类型的实例和类型间的冒号前不需要有空格。

	interface Foo<out T : Any> : Bar {
	    fun foo(a: Int): T
	}

## 3.Lambda 表达式 ##
在 lambda 表达式中，花括号和箭头周围应该有一个空格；另外，lambda 尽量在圆括号外。

	list.filter { it > 10 }.map { element -> element * 2 }

lambda 表达式应该简短不嵌套，推荐使用 it 来代替明确地声明参数；在嵌套的 lambda 表达式中，参数应该明确声明。

## 4.class 头部风格 ##
如果一个 class 仅有几个参数，那么这个 class 可以写成一行。

	class Person(id: Int, name: String)

当一个 class 有多个参数时，这些参数应该分别另起一行并且缩行显示，另外这个 class 的 ）也要另起一行；当这个 class 继承自另一个 class 或 实现接口时，其父类的参数应该在一行。

	class Person(
	    id: Int, 
	    name: String,
	    surname: String
	) : Human(id, name) {
	    // ...
	}

在继承 class 和实现接口同时出现的情况，其父类应该写在前面，接口写在后面。

	class Person(
	    id: Int, 
	    name: String,
	    surname: String
	) : Human(id, name),
	    KotlinMaker {
	    // ...
	}

另外，构造函数中的参数也可以双倍缩进，即用 8 个空格缩进。

## 5.Unit ##

如果一个函数的返回值是 Unit ，则返回类型应该省略。

	fun foo() {
		...
	}

## 6.函数和属性的优先使用规则 ##
在一些情况下，函数和只读属性是可互换的。虽然它们的语意相似，但是有时使用属性更好，在这些情景中优先使用属性，而不使用函数：

- 没有异常抛出；
- 空间复杂度为 O(1) ；
- 计算量小 (或者第一次运行后有缓存)
- 每次执行后的返回值都相同

