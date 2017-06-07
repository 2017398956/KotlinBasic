# Kotlin 从学习到 Android 第八章 接口 #
## 接口 ##
Kotlin中的接口与Java 8非常相似。它们可以包含抽象方法的声明，以及方法实现。与抽象类不同，接口不能存储状态。它们可以具有属性，但这些属性必须是抽象的，或者提供存取器实现。

定义接口使用关键字 interface ：

	interface MyInterface {
	    fun bar()
	    fun foo() {
	      // 函数体是可选的
	    }
	}

## 接口的实现 ##
一个类或对象可以实现一个或多个接口：

	class Child : MyInterface {
	    override fun bar() {
	        ...
	    }
	}

## 接口中的属性 ##
在接口中声明的属性可以是抽象的，如果不是抽象的则要为它实现存取器。接口中的属性不能有 backing field ，因此在接口中声明的存取器不能引用它们，关于这一点直接从文字描述不太好理解，请看下面代码：

	fun main(args: Array<String>) {
	    var child: Child = Child()
	    var fields: Array<Field> = child.javaClass.declaredFields;
	    for (field in fields) {// 只有一个值 prop
	        println(field.name)
	    }
		// 由于 propertyWithImplementation 是非 backing field 的，所以下面的语句不起效果
		child.propertyWithImplementation = "sdafasfsda" 
		println(child.propertyWithImplementation) // foo
	    child.prop = 30	// 由于 Child 中覆写了该属性，且可为 backing field ，所以可以赋值
		println(child.prop) // 30
	}
	
	interface MyInterface {
	    var prop: Int // 抽象属性
	
	    var propertyWithImplementation: String // 非抽象属性，且非 backing field
	        get() = "foo"
	        set(value) {
				// 如果强制声明为 field 则，编译器提示错误，无法编译
				// field = value
	        }
	}
	
	class Child : MyInterface {
		// 抽象属性必须覆写
	    override var prop: Int = 29
		// 如果这里覆写了 propertyWithImplementation 并是 backing field 的，则上面打印结果就不再是 foo 而是 sdafasfsda ， fieds 中也会多一个 propertyWithImplementation
		// override var propertyWithImplementation: String = ""
		//         set(value) {
		//             field = value
		//         }
	}


## 覆写冲突 ##
当一个类实现多个接口时，这些接口中可能出现多个同名方法的实现。此时我们可以使用 super<类名>.method() 的方式来区分调用的是哪个父类的哪个方法，例如：

	interface A {
	    fun foo() { print("A") }
	    fun bar()
	}
	
	interface B {
	    fun foo() { print("B") }
	    fun bar() { print("bar") }
	}
	
	class C : A {
	    override fun bar() { print("bar") }
	}
	
	class D : A, B {
	    override fun foo() {
	        super<A>.foo()
	        super<B>.foo()
	    }
	
	    override fun bar() {
	        super<B>.bar()
	    }
	}