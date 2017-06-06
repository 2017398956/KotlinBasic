# Kotlin 从学习到 Android 第七章 属性和字段 #
## 声明属性 ##
在 Kotlin 中可以用 var 声明可修改属性，也可以用 val 声明只读属性：

	class Address {
	    var name: String = ...
	    var street: String = ...
	    var city: String = ...
	    var state: String? = ...
	    var zip: String = ...
	}

我们可以直接通过属性名使用属性值，就像 java 中字段一样：

	fun copyAddress(address: Address): Address {
	    val result = Address() // there's no 'new' keyword in Kotlin
	    result.name = address.name // accessors are called
	    result.street = address.street
	    // ...
	    return result
	}

## get 和 set 函数 ##
完整的属性声明语句如下：

	var <propertyName>[: <PropertyType>] [= <property_initializer>]
	    [<getter>]
	    [<setter>]

其中初始化值，get 和 set 函数是可选的。如果系统可以根据初始化值（或 get 函数的返回值）推断出属性的类型，那么属性类型也是可选的，例如：

	var allByDefault: Int? // 错误: 需要明确初始化值, 隐含着默认的 getter and setter
	var initialized = 1 // 可推导出数据类型为 Int, 含有默认的 getter and setter

只读属性和可修改属性的完整声明不同，它没有 setter ：

	val simple: Int? // 有属性类型 Int, 默认的 getter, 必须在构造函数中初始化
	val inferredType = 1 // 属性类型为 Int 并有一个默认的 getter

我们也可以自定义 get 和 set 函数，只需要在属性右边像声明一般函数那样声即可：

	var stringRepresentation: String
	    get() = this.toString()
	    set(value) {
	       ...
	    }

按照 Kotlin 的语法规范，setter 的参数名一般都是 value ，当然你也可以定义一个其它名称的参数。

从 Kotlin 1.1 开始，如果一个属性的类型可以从 getter 推断出来，那么在声明时可以省略属性类型的声明：

	val isEmpty get() = this.size == 0  // Boolean

如果你只想更改 getter 或 setter 的访问权限或添加注解，而不改变它们的默认实现方式，那么你可以像下面这样不写它们的函数体：

	var setterVisibility: String = "abc"
	    private set // 将 setter 修改为 private 权限
	
	var setterWithAnnotation: Any? = null
	    @Inject set // 为 setter 添加注解

## Backing Fields ##













