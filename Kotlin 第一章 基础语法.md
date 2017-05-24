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
在 java 中，数字类型是 JVM 私有的类型，当我们使用泛型或需要使其为 null 时，需要对其进行装箱操作（从 J2SE 5.0 可以自动装箱拆箱 ）；而在 Kotlin 中如果一个基本数据类型（包括 Boolean 等）是可 null ，即使用了 “？” 声明，那么就会被自动装箱。

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
### 1.9 String ###

## 2. 定义包名 ##
包名应该在文件头部

	package my.demo
	import java.util.*
	// ...

**注意**：在 kotlin 中包名可以不和文件路径相对应。