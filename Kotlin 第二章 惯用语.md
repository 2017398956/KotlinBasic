# Kotlin 从学习到 Android 第二章 习惯用法 #
## 1.数据类 ##
相当于 java 中的 bean ，用关键字 data 来声明这个 class ；

	data class User(val name: String, val age: Int)
	var user:User = User("admin" , 20)

由于比较模版化（如：set 、get 等），所以在 Kotlin 中如下的几种方法是系统自动生成的：

- equals()/hashCode() 
- toString() of the form "User(name=John, age=42)",
- .属性名 （如： user.age）
- copy() 

如果这些方法在 class 中被复写，或该 class 继承的 class 中有这些方法，那么系统不会再次生成这些方法。

为了确保数据类自动生成代码的正确性，数据类必须符合下面几种条件：

- 主要构造方法至少含有一个参数;
- 所有的主要构造方法参数必须用 val 或 var 声明;
- 数据类不能用 abstract, open, sealed 或 inner 修饰;
- (1.1 之前的版本) 数据类只能实现接口；
- 从 1.1 开始, 数据类可以继承自其它类；

如果数据类想要使用无参构造方法，那么其有参构造方法必须指定初始值。

	fun main(args: Array<String>) {
	    var user:User = User()
	    println(user.name)      // admin
	}
	
	data class User(val name: String = "admin", val age: Int? = 21)

**copy()方法的使用**
在很多情况下，我们需要复制一个类，然后修改其中的某一个或几个属性的值，而其他属性的值不变，这时就可以使用 copy() 了。

	val jack = User(name = "Jack", age = 1)
	val olderJack = jack.copy(age = 2)
	println(olderJack.toString()) 			// User(name=Jack, age=2)

**数据类和[结构声明](http://kotlinlang.org/docs/reference/multi-declarations.html)**

	val jane = User("Jane", 35) 
	val (name, age) = jane
	println("$name, $age years of age") // Jane, 35 years of age

## 2.创建 DTOs (POJOs/POCOs) ##

	data class Customer(val name: String, val email: String)

## 3.函数参数默认值 ##

	fun foo(a: Int = 0, b: String = "") { ... }

## 4.过滤 list ##

	val positives = list.filter { x -> x > 0 }

或者更简单的写法

	val positives = list.filter { it > 0 }

## 5.字符串插入 ##

	println("Name $name")

## 6.实例检查 ##

	when (x) {
	    is Foo -> ...
	    is Bar -> ...
	    else   -> ...
	}

## 7.遍历 map / list ##

	for ((k, v) in map) {
	    println("$k -> $v")
	}
	// k , v 名字随意起，符合 Kotlin 命名规范即可

## 8.使用范围 ##

	for (i in 1..100) { ... }  // 闭合范围包括 100
	for (i in 1 until 100) { ... } // 半开放范围不包括 100
	for (x in 2..10 step 2) { ... }
	for (x in 10 downTo 1) { ... }
	if (x in 1..10) { ... }

## 9.只读列表 ##

	val list = listOf("a", "b", "c")

## 10.只读 map ##

	val map = mapOf("a" to 1, "b" to 2, "c" to 3)

## 11.map 的访问 ##

	println(map["key"])
	map["key"] = value

## 12.懒属性 ##

	val p: String by lazy {
	    // compute the string
	}

## 13.扩展函数 ##

	fun String.spaceToCamelCase() { ... }
	
	"Convert this to camelcase".spaceToCamelCase()

## 14.创建单例 ##

	object Resource {
	    val name = "Name"
	}

## 15.If not null shorthand ##

	val files = File("Test").listFiles()
	
	println(files?.size)

## 16.If not null and else shorthand ##

	val files = File("Test").listFiles()
	
	println(files?.size ?: "empty")

## 17.数据为 null 时执行语句 ##

	val data = ...
	val email = data["email"] ?: throw IllegalStateException("Email is missing!")


## 18.数据不为 null 时执行语句 ##

	val data = ...
	
	data?.let {
	    ... // execute this block if not null
	}

## 19.用 when 返回函数返回值 ##

	fun transform(color: String): Int {
	    return when (color) {
	        "Red" -> 0
	        "Green" -> 1
	        "Blue" -> 2
	        else -> throw IllegalArgumentException("Invalid color param value")
	    }
	}

## 20.try/catch ##

	fun test() {
	    val result = try {
	        count()
	    } catch (e: ArithmeticException) {
	        throw IllegalStateException(e)
	    }
	
	    // Working with result
	}

## 21.if 表达式 ##

	fun foo(param: Int) {
	    val result = if (param == 1) {
	        "one"
	    } else if (param == 2) {
	        "two"
	    } else {
	        "three"
	    }
	}

## 22.Builder 风格函数的使用 ##
	
	fun arrayOfMinusOnes(size: Int): IntArray {
	    return IntArray(size).apply { fill(-1) }
	}

## 23.使用 with 关键字在一个实例中上一次调用多个方法 ##

	class Turtle {
	    fun penDown()
	    fun penUp()
	    fun turn(degrees: Double)
	    fun forward(pixels: Double)
	}
	
	val myTurtle = Turtle()
	with(myTurtle) { 
	    penDown()
	    for(i in 1..4) {
	        forward(100.0)
	        turn(90.0)
	    }
	    penUp()
	}

## 24.Java 7 使用资源 ##

	val stream = Files.newInputStream(Paths.get("/some/file.txt"))
	stream.buffered().reader().use { reader ->
	    println(reader.readText())
	}

## 25.泛型的使用 ##

	//  public final class Gson {
	//  	...
	//     public <T> T fromJson(JsonElement json, Class<T> classOfT) throws JsonSyntaxException {
	//     ...
	
	inline fun <reified T: Any> Gson.fromJson(json): T = this.fromJson(json, T::class.java)

## 26.声明一个可 null 的 Boolean 类型数据 ##

	val b: Boolean? = ...
	if (b == true) {
	    ...
	} else {
	    // b 是 false 或者 null 进入此逻辑
	}
