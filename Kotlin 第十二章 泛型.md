# Kotlin 从学习到 Android 第十二章 泛型 #
与 Java 一样，Kotlin 中的类可以具有类型参数:

	class Box<T>(t: T) {
	    var value = t
	}

通常，要创建此类类的实例，我们需要提供类型参数:

	val box: Box<Int> = Box<Int>(1)

但是，如果参数可以被推断出来（例如，从构造函数参数或者其他方法中可以推断出），那么可以省略类型参数:

	val box = Box(1) // 1 的类型是 Int, 因此编译器会推断出我们正在使用 Box<Int>

Java类型系统中最棘手的部分是通配符类型(请参阅 [Java Generics FAQ](http://www.angelikalanger.com/GenericsFAQ/JavaGenericsFAQ.html) )，但是 Kotlin 中没有这种问题。另外，Kotlin 中的泛型还有另外两个特性: declaration-site variance 和 type projections。

那么，让我们思考一下：为什么 java 需要通配符？这个问题在 [Effective Java, Item 28: Use bounded wildcards to increase API flexibility](http://www.oracle.com/technetwork/java/effectivejava-136174.html) 有详细的解释。 首先, Java 中的泛型类型是不变的, 这意味着 List<String> 不是 List<Object> 的子类。 为什么这样? 如果 List 是可变的, 那么下面的代码虽然可以编译时发生异常:

	// Java
	List<String> strs = new ArrayList<String>();
	List<Object> objs = strs; // java 禁止这么转换，编译时会报 不兼容的类型 错误
	objs.add(1); 
	String s = strs.get(0); // ClassCastException: Cannot cast Integer to String

因此，Java 禁止这样的事情来保证运行时安全性。但是，这会造成一些其它的影响，例如：我们不能通过 Collection 接口中的 addAll() 方法，

	// Java
	interface Collection<E> ... {
	  void addAll(Collection<E> items);
	}

来实现以下如此简单的事情(虽然这是完全安全的):

	// Java
	void copyAll(Collection<Object> to, Collection<String> from) {
	  to.addAll(from); // !!! Would not compile with the naive declaration of addAll:
	                   //       Collection<String> is not a subtype of Collection<Object>
	}