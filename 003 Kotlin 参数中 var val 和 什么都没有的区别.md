# Kotlin 参数中 var val 和 什么都没有的区别

kotlin 的方法分为构造方法和一般方法，在构造方法中参数有三种声明方式：

- 使用 var 声明
- 使用 val 声明
- 声明时什么都不加

在一般方法中，之前的版本可以使用 var ，现在都统一默认时 val 且不能更改。下面针对这两类方法分别分析这些声名形式的作用。

## 一、构造方法

### 1.1 使用 val 声明

```kotlin
open class Person(name:String) {
}

class Student(val name: String, val age:Int) : Person(name) {
	// 注意看 Student 转换的 java 代码
}
```

转换为 Java 代码：

```java
public final class Student extends Person{

  @NotNull
  private final String name;
  private final int age;

  public Student(@NotNull String name, int age){
    super(name); 
	this.name = name; 
	this.age = age; 
  } 
  @NotNull
  public final String getName() { 
	return this.name; 
  } 
  public final int getAge() { 
	return this.age;
  }
}
```

可以看到转换成 java 代码后用 val 声明的参数会在 class 中自动生成一个 private final 的属性和一个 final 的 getter 方法。

### 1.2 使用 var 声明

```kotlin
class Student(var name: String, var age:Int) : Person(name) {
	// 注意看 Student 转换的 java 代码
}
```

转换为 Java 代码：

```java
public final class Student extends Person{

  @NotNull
  private String name;
  private int age;

  public Student(@NotNull String name, int age){
    super(name); 
	this.name = name; 
	this.age = age; 
  } 
  @NotNull
  public final String getName() { 
	return this.name; 
  } 
  public final void setName(@NotNull String <set-?>) { 
    	Intrinsics.checkNotNullParameter(<set-?>, "<set-?>"); 
		this.name = <set-?>; 
  } 
  public final void setAge(int <set-?>) { 
	this.age = <set-?>;
  }
  public final int getAge() { 
	return this.age;
  }
}
```

可以看到转换成 java 代码后用 var 声明的参数会在 class 中自动生成一个 private 的属性和一对 final 的 getter、setter 方法。

### 1.3 什么都不加

```kotlin
class Student(name: String, age:Int) : Person(name) {
	// 注意看 Student 转换的 java 代码
}
```

转换为 Java 代码：

```java
public final class Student extends Person{

  public Student(@NotNull String name, int age){
    super(name); 
  } 
}
```

可以看到转换成 java 代码后 class 中没有添加任何属性，Student 构造方法中的 age 参数，只是简单地传递给父类，而 age 没有做任何处理。

## 二、一般方法

一般方法的参数，在之前的版本中是可以用 var 声明的，后面都统一默认是 val 切不可修改。为什么会这么做呢？

在 java 中我们常碰到这样一道面试题：

```java
class Test {
	private int a;
	private List list = new ArrayList();
	public void test(int a, List list) {
		// 对 a 和 list 一通操作
	}

	public void invokeTest() {
		test(a, list);
	}
}
```

面试官往往会问你执行完 invokeTest() 后 Test 中的变量 a 和 list 的值是怎么样的，相信大家都能答出来。现在在 kotlin 中，由于默认的都是 val，所以对于初学者就没有这个问题了，再也不用疑惑什么时候 class 属性可以改变，什么时候不会改变了，只要是 val 的都不可改变。

下面是官方的解释：

```
The main reason is that this was confusing: people tend to think that
this means passing a parameter by reference, which we do not support
(it is costly at runtime). Another source of confusion is primary
constructors: “val” or “var” in a constructor declaration means
something different from the same thing if a function declarations
(namely, it creates a property). Also, we all know that mutating
parameters is no good style, so writing “val” or “var” in front of a
parameter in a function, catch block of for-loop is no longer allowed.
```
