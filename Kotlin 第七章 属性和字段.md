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

## getter 和 setter ##
完整的属性声明语句如下：

	var <propertyName>[: <PropertyType>] [= <property_initializer>]
	    [<getter>]
	    [<setter>]

其中初始化值，getter 和 setter 是可选的。如果系统可以根据初始化值（或 getter 的返回值）推断出属性的类型，那么属性类型也是可选的，例如：

	var allByDefault: Int? // 错误: 需要明确初始化值, 隐含着默认的 getter and setter
	var initialized = 1 // 可推导出数据类型为 Int, 含有默认的 getter and setter

只读属性和可修改属性的完整声明不同，它没有 setter ：

	val simple: Int? // 有属性类型 Int, 默认的 getter, 必须在构造函数中初始化
	val inferredType = 1 // 属性类型为 Int 并有一个默认的 getter

我们也可以自定义 getter 和 setter ，只需要在属性右边像声明一般函数那样声即可：

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
Kotlin 中的类不能含有 fields 。然而，有时当我们使用自定义存取器的时候需要 backing fields 。所以，Kotlin 提供了一个自动的 backing fields ，可以通过关键字 field 进行存取：

	var counter = 0 // 初始化值被直接写给 backing fields
	    set(value) {
	        if (value >= 0) field = value
	    }

**注意**： field 关键字只能用在属性的存取上。

如果属性至少使用了一个默认的存取器或者这个属性在自定义的存取器中声明了 field ，那么系统会自动为它生成一个 backing field 。

例如，下面的情况将不会有 backing field （因为 isEmpty 是只读的没有 setter ，自定义的 getter 中又没有声明 field ）：

	val isEmpty: Boolean
	    get() = this.size == 0

如果将 val 换成 var ，虽然自定义的 getter 没有声明 field ，但是 isEmpty 属性有默认的 setter ，所以有 backing field：

	var isEmpty: Boolean = true
		    get() = this.size == 0

下面可以对以上结论进行证明：

	fun main(args: Array<String>) {
	    var test: Test = Test()
	    var fields: Array<Field> = test.javaClass.declaredFields ;
	    for(field in fields){
	        println(field.name)
	    }
	}
	
	class Test {
	    var counter = 0 // the initializer value is written directly to the backing field
	        set(value) {
	            if (value >= 0) field = value
	        }
	
	    var isEmpty: Boolean = true
	        get() = 1 == 0
	
	    val isNumber: Boolean
	        get() = 1 == 0
	}

打印结果：

	counter
	isEmpty

## Backing Properties ##
如果你想做一些操作，且这些操作不适合具有 backing field 的性质，那么你可以这么做：

	private var _table: Map<String, Int>? = null
	public val table: Map<String, Int>
	    get() {
			// 这里的操作不希望属于 backing filed，
	        if (_table == null) {
	            _table = HashMap() 
	        }
	        return _table ?: throw AssertionError("Set to null by another thread")
	    }

这就像 java 中私有属性的默认 getter 和 setter ，这样就没有其它函数可以介入对这个属性的操作了。

## 编译时常量 ##
编译时可以确定值的属性可以用 const 修饰来表明它是编译时常量，这样的属性必须符合以下的条件：

- 对象的顶级属性或成员
- 以 String 类型或原始类型初始化
- 没有自定义的 getter

编译时常量可以用在注解中:

	const val SUBSYSTEM_DEPRECATED: String = "This subsystem is deprecated"
	
	@Deprecated(SUBSYSTEM_DEPRECATED) fun foo() { ... }

## 迟初始化属性 ##
一般情况下，如果属性被声明为非 null 的，那么就必须在构造函数中初始化。然而，这样十分不方便。例如，属性可能通过注解，或单元测试中的方法初始化。在这些情况下，你不能实现在构造函数中初始化，但是你仍然希望在这个属性在所在的类中被安全的使用，即不用判断非 null 。
为了解决这种情况，你可以使用 lateinit 来修饰这个属性：

	public class MyTest {
	    lateinit var subject: TestSubject
	
	    @SetUp fun setup() {
	        subject = TestSubject()
	    }
	
	    @Test fun test() {
	        subject.method()  // dereference directly
	    }
	}
lateinit 的使用条件必须满足：

- 类体中用 var 声明的属性且不在主构造函数中
- 没有自定义的 getter 或 setter
- 属性类型非 null
- 不能为原始类型

在一个 lateinit 属性初始化前访问它将会抛出异常：

	kotlin.UninitializedPropertyAccessException: lateinit property subject has not been initialized

## 覆写属性 ##
见第六章
## [委托属性](http://kotlinlang.org/docs/reference/delegated-properties.html) ##
最常见的属性只是从(可能写入)一个支持字段中读取。另一方面，使用自定义getter和setter方法可以实现属性的任何行为。在介于两者之间，有一些共同的模式来说明一个属性是如何工作的。例如:lazy values 、根据 key 从 map 中读取值、访问数据库、通知侦听器访问等等。
这些常见的行为可以作为使用委托属性的库来实现。