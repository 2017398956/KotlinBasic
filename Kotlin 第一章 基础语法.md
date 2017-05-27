# 第一章 基础语法 #
## [1. 基本数据类型](http://kotlinlang.org/docs/reference/basic-types.html) ##
### 1.1 数字类型 ###

| 数据类型 | 字节长度 |
|---------|---------|
| Double  |	8       |
| Float	  | 4       |
| Long	  | 8       |
| Int	  | 4       |
| Short	  | 2       |
| Byte	  | 1       |

**注意**：且 Kotlin 的字符类型不能转化为数字。
	
	// java 代码下面将输出 98
	System.out.println('a' + 1) ;

	// kotlin 代码下面将输出 b
	println('a' + 1) 
### 1.2 数字的表示 ###
和 java 中一样，不同数据类型有其特有的书写方式，其格式如下：
- Long ： 123L ，不能用 “l” （小写 L）
- 十六进制： 0x11 ，对应十进制 17
- 二进制：0b11 ，对应十进制 3
- Double ： 1.2e3 ，对应十进制 1.3 * 10^3
- Float ： 123.4f 或 123.4F

**注意**： Kotlin 不支持八进制。
### 1.3 数字中的下划线 ###
为了方便阅读位数很多的数字，从 Kotlin 1.1 版后可以在数字中加入下划线来分割，使用如下：

	val oneMillion = 1_000_000
	val creditCardNumber = 1234_5678_9012_3456L
	val socialSecurityNumber = 999_99_9999L
	val hexBytes = 0xFF_EC_DE_5E
	val bytes = 0b11010010_01101001_10010100_10010010
### 1.4 拆箱与装箱 ###
在 java 中，数字类型是 JVM 私有的类型，当我们使用泛型或需要使其为 null 时，需要对其进行装箱操作（从 J2SE 5.0 可以自动装箱拆箱 ）；而在 Kotlin 中如果一个基本数据类型（包括 Boolean 等）是可 null ，即：使用了 “？” 声明，那么就会被自动装箱。

	val a: Int = 10000
	print(a === a) // Prints 'true'
	val boxedA: Int? = a
	val anotherBoxedA: Int? = a
	// “===”  比较的是地址
	print(boxedA === anotherBoxedA) // !!!Prints 'false'!!!
	// “==”  比较的是值
	print(boxedA == anotherBoxedA) // Prints 'true'
**注意**：当 “===” 比较的对象是 Int 类型且其值在 [-128 , 127] 时（即: Byte 的取值范围），Kotlin 返回的是 JVM 的内置属性，也就是说此时 “===” 的返回结果为 true 。
### 1.5 操作符 ###
- shl(bits) – 等同 java 中的 << ， 如： 2 shl 1
- shr(bits) – 等同 java 中的 >>
- ushr(bits) – 等同 java 中的 >>>
- and(bits) – 等同 java 中的 and
- or(bits) – 等同 java 中的 or
- xor(bits) – 等同 java 中的 xor
- inv() – 等同 java 中的 inversion

### 1.6 字符型 ###
字符型在 Kotlin 中用 Char 表示，不能像 java 中那样直接当做数字来使用，例如：

	fun check(c: Char) {
    	if (c == 1) { // ERROR: 这样不能编译通过
        	// ...
    	}
	}
既然 Char 类型无法当做数字来使用，那么当我们需要像 java 中那样使用 char 对应的数字时可以这么做：

	fun decimalDigitValue(c: Char): Int {
    	if (c !in '0'..'9')
        	throw IllegalArgumentException("Out of range")
    	return c.toInt() - '0'.toInt() // Explicit conversions to numbers
	}
### 1.7 Boolean ###
Boolean 有两个取值 true 或 false 。
### 1.8 Array ###
Array 类型在 Kotlin 中创建比较自由，常用的的方式有如下几种：

	// 1. 使用 arrayOf() 创建
	val list01 : Array<Int> = arrayOf(1 , 2 , 3)
	// 2. 创建一个指定长度的 null Array
	val list02 : Array<Int> = arrayOfNulls<Int>(3) 
	// 3. 创建数组 ["0", "1", "4", "9", "16"]
	val list03 = Array(5, { i -> (i * i).toString() })
	// 4. 不用泛型创建（Boolean 、 Float 等）
	val list04: IntArray = intArrayOf(1, 2, 3)


### 1.9 String ###
在 Kotlin 中 String 类型可以像 Array 类型那样通过迭代的方式挨个字符输出，如：

	val str: String = "abc"
	for(c in str){
		println(c)
	}
	// 打印结果
	// a
	// b
	// c
另外，String 在 Kotlin 中除了有像 java 中一样的声明方式，又添加了一种新的声明方式：

	// 1. 与 java 相同的声明方式,特殊字符使用反斜杠
	val str01 = "Hello \"World\" !\n"  
	// 2. Kotlin 特有的声明方式：三个双引号开始至三个双引号结束
	val text = """
    	for (c in "foo")
        	print(c)
	"""
	// 这种方式两组双引号间的内用会原封不动的作为一个字符串包括特殊字符和空格
	
除了新的声明方式，Kotlin 还为 String 添加了一种以 "$" 作为开头的引入方式：

	val text01 = "10"
    val text02 = """abcd${text01}"""
    println("${text01.length}")			// 10
    println("$text02")				    // abcd10
## [2. 定义包名](http://kotlinlang.org/docs/reference/basic-syntax.html) ##
包名应该在文件头部，如果不声明 Kotlin 文件所在的包，那么该文件将没有包名，被不同包下的函数调用时只需要 import funXXX 即可。

	// 1. file01.kt
	package my.demo
	import java.util.*
	import noPackageNameFun
	// ...
	noPackageNameFun()

	// 2. file02.kt
	fun noPackageNameFun(){...}

**注意**：在 kotlin 中包名可以不和文件路径相对应。

Kotlin 也会像 java 那样默认导入一些包，这样我们就可以直接在文件中使用了，默认导入的包有：

- kotlin.*
- kotlin.annotation.*
- kotlin.collections.*
- kotlin.comparisons.* (since 1.1)
- kotlin.io.*
- kotlin.ranges.*
- kotlin.sequences.*
- kotlin.text.*

当针对不同平台时，还会默认导入一些平台特有的包，如：

**JVM**
- java.lang.*
- kotlin.jvm.*

**JS**
- kotlin.js.*

### [2.1 包](http://kotlinlang.org/docs/reference/packages.html) ####
在 Kotlin 中导包操作和 java 有很大的不同：

1. 命名冲突的处理

		// 命名冲突时，可以用 as 来重命名
		import foo.Bar
		import bar.Bar as bBar

2. 除了 class 外，import 在 Kotlin 中还可以导入：
- 顶级函数和属性
- [对象声明](http://kotlinlang.org/docs/reference/object-declarations.html)中的函数和属性
- 枚举常量

另外，Kotlin 没有 import static ，所有情况都用 import 即可。

**注意**：如果顶级的声明用了 private ，那么它只能在所声明的文件中使用。

### [2.2 函数](http://kotlinlang.org/docs/reference/functions.html) ###
在 Kotlin 中函数的声明要用 fun ，一般有如下几种方式：
1. 声明返回类型

		fun sum(a: Int, b: Int): Int {
    		return * // 一个 Int 类型的数字
		}

2. 对于表达式构成的函数，返回一个推测类型

		fun sum(a: Int, b: Int) = a + b // 这里会返回 a 、 b 之和且为 Int 类型

3. 返回一个无意义的值

		// 此函数的返回值为： kotlin.Unit
		fun printSum(a: Int, b: Int): Unit {
			... // 函数体中如果有 return ，其后不能跟任何有意义的值
		}

4. 当返回值为 Unit 时，可以再函数声明中省略，上面的函数等效于：

		// 此函数的返回值为： kotlin.Unit
		fun printSum(a: Int, b: Int){
			... // 函数体中如果有 return ，其后不能跟任何有意义的值
		}

#### 2.2.1 中缀表达式 ####
当符合下面三种情况时，函数也可以使用中缀表达式的方式声明（并没看出有什么用 ... ）:

- 成员函数或[扩展函数](http://kotlinlang.org/docs/reference/extensions.html)
- 函数有且只有一个参数
- 函数声明关键字 fun 前用 infix 修饰
		
使用函数如下:

	class CustomObject{
	
	}
	infix fun CustomObject.plus100(x : Int): Int{
	    return x + 100
	}
	infix fun Int.test(x : Int): Int{
		...
	}
	CustomObject() plus100 20 		// 与下面的等效
	CustomObject().plus100(20)

	1 test 2						// 与下面的等效
	1.test(2)

#### 2.2.2 参数 ####
函数参数使用 Pascal 表达式的格式声明，也就是 参数名: 参数类型 的格式。参数间以逗号分隔，且参数必须指定参数类型。

	fun powerOf(number: Int, exponent: Int) {
		...
	}

#### 2.2.3 函数参数的默认值 ####
在 Kotlin 中，可以直接对参数进行初始化：

	open class A {
	    open fun foo(i: Int = 10) {
	        println("A:" + i)
	    }
	}

	class B : A() {
	    override fun foo(i: Int) {
	        super.foo(i)
	        println("B:" + i)
	    }
	}

	fun read(b: Array<Byte>, off: Int = 0, len: Int = b.size()) {
		println(off) // 0
		...
		B().foo() // 不传参数时会使用默认值
		// A:10
		// B:10
		B().foo(2)
		// A:2
		// B:2
	}

#### 2.2.4 Named Arguments ####
对于一个有很多参数的函数，有时我们仅传入一个参数，而其他参数都使用默认值，在 java 中你需要把其他每个参数的默认值按顺序写一遍；现在，在 Kotlin 中不需要那么做了，我们可以只关注那些需要修改的参数，例如：

	// 一个大量参数的函数
	fun reformat(str: String,
	             normalizeCase: Boolean = true,
	             upperCaseFirstLetter: Boolean = true,
	             divideByCamelHumps: Boolean = false,
	             wordSeparator: Char = ' ') {
	...
	}
	// 当我们只关注 str ，其他参数使用默认值时，可以这样调用
	reformat(str)
	// 可以这样，直接跳过中间几个参数
	reformat("abc" , wordSeparator = 'a')
	// 还可以这样，不按照声明的顺序
	reformat("abc" , wordSeparator = 'a' , normalizeCase = false)
	// 但不能这样
	// reformat("abc" , 'a') // 错误的写法
#### 2.2.5 函数返回 kotlin.Unit ####
当一个函数无需返回任何有意义的值时，类似于 java 中的 void ，但不同的是：这个函数会返回一个唯一值 kotlin.Unit ，且返回类型 Unit 可以省略。
	
	fun myPrint(str : String){
		...
	}
	// 等效于
	fun myPrint(str : String): Unit{
		...
	}

#### 2.2.6 单一表达式 ####
当一个函数的返回值是一个表达式语句时，大括号可以省略，将表达式置于 = 后即可。

	fun double(x: Int): Int = x * 2

如果返回值类型是可以推测的也可以省略：

	fun double(x: Int) = x * 2

#### 2.2.7 明确函数返回类型 ####
当函数的函数体用大括号括起来的时候必须指定函数的返回值类型，除非函数的返回值是 Unit ，此时可以省略不写；因为，当函数体有大括号时，Kotlin 不会推测函数返回值得类型。

#### 2.2.8 不定长参数 ####
不定长参数类似于 java 中的 void method(String ...){...}，当一个函数的参数是不定长参数时，使用关键字 vararg  声明。

	fun <T> asList(vararg ts: T): List<T> {
	    val result = ArrayList<T>()
	    for (t in ts) // ts 可以看做是个数组，可以 ts[0] 这样使用其中的元素，注意数组的下标越界
	        result.add(t)
	    return result
	}
	val list01 = asList(1, 2, 3)
	// 当需要为不定长传入一个已经有的数组时，我们可以用 * 表示
	val list02 = asList(1, 2, *list01 , 3)

**注意**：当不定长参数不是最后一个参数时，可以使用 named argument （见 2.2.4）。

#### 2.2.9 函数的使用范围 ####
在 Kotlin 中，函数可以直接在文件中声明，而不需要依赖 class 存在。例如下面的 Hello.kt 文件：

	// Hello.kt 文件中只有一个函数
	fun sayHello(){
		println("Hello")
	}
另外，Kotlin 函数也能作为 局部函数 、 成员函数 和 扩展函数。

**局部函数**

	// 函数嵌套在另一个函数中，并能访问这个函数外的变量
	val name: String = "kotlin"
	fun main(args: Array<String>) {
		val str : String = "str"
	    fun localFun(str:String){
	        println("local fun $name " + str)
	    }
	    localFun()
	}

**成员函数**

成员函数就是定义在 class 中的函数。

	class Sample() {
	    fun foo() { print("Foo") }
	}

**泛型**

和 java 中的泛型大同小异。

	fun <T> singletonList(item: T): List<T> {
	    // ...
	}

**[内联函数](http://kotlinlang.org/docs/reference/inline-functions.html)**

**[Extension Functions](http://kotlinlang.org/docs/reference/extensions.html)**

**[Higher-Order Functions and Lambdas](http://kotlinlang.org/docs/reference/lambdas.html)**

**尾递归函数**

关于什么是尾递归函数可以看[这篇](http://www.cnblogs.com/Anker/archive/2013/03/04/2943498.html)，这里只讲一下如何在 Kotlin 中使用。

	tailrec fun findFixPoint(x: Double = 1.0): Double
	        = if (x == Math.cos(x)) x else findFixPoint(Math.cos(x))

使用尾递归需要在关键字 fun 前添加 tailrec ，另外，使用尾递归时这个函数必须在最后调用自身，你不能在 try/catch/finally 中使用尾递归，而且当前尾递归（version 1.1）仅在 JVM 后端支持。
## [3.定义局部变量](http://kotlinlang.org/docs/reference/properties.html) ###
**只读局部变量**

	// 只读变量用关键字 val 声明
	val a: Int = 1  // 声明的时候立刻初始化
	val b = 2   // 编译器会推测 b 是 Int 类型
	val c: Int  // 声明时不初始化，必须指定变量类型
	c = 3       // 声明后赋值
	// 对于只读变量只能初始化时赋值一次，下面的代码如果不注释则编译器会给出提示编译不通过
	// a = 2

**可变局部变量**

	// 可变变量用关键字 var 声明
	var x = 5 // 编译器会推测 x 是 Int 类型
	x += 1

**注意**：在 Kotlin 中没有关键字 new ，所以创建对象时可以直接调用构造方法：

	class A{
		...
	}
	var a = A()

## 4.注释 ##

和 java 一样，Kotlin 支持也单行注释和多行注释，但是 Kotlin 支持多行注释间的嵌套。

	// This is an end-of-line comment
	
	/* This is a block comment
	   on multiple lines. */
	
	// 多行注释的嵌套
	/* This is a block comment	/* This is a block comment
		   on multiple lines. */
	            on multiple lines. */

## 5.条件表达式 ##
同 java 。
## 6.null 安全 ##
在 java 中，稍不注意就会报 NullPointException ，而在 Kotlin 中完全不用担心，我们可以使用 ？ 标识对象获属性石 null 安全的。

	fun main(args : Array<String>){
		fun testNullSafety() : String?{
			return null
		}
		// 这里并不会报 NPE
		println(testNullSafety().toString())
	}