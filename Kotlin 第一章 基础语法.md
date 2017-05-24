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
	// 这种方式两组双引号间的内用会原封不动的作为一个字符串包括特殊字符和空格，如果想去掉空格，使用 
	
除了新的声明方式，Kotlin 还为 String 添加了一种以 "$" 作为开头的引入方式：

	val text01 = "10"
    val text02 = """abcd${text01}"""
    println("${text01.length}")			// 10
    println("$text02")				    // abcd10
## [2. 定义包名](http://kotlinlang.org/docs/reference/basic-syntax.html) ##
包名应该在文件头部

	package my.demo
	import java.util.*
	// ...

**注意**：在 kotlin 中包名可以不和文件路径相对应。