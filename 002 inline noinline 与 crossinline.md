# inline、noinline 与 crossinline

## 一、高阶函数

在了解 inline 等之前，首先来看下 kotlin 中关于高阶函数的定义：**如果一个函数接收另一个函数作为参数，或返回类型是一个函数类型，那么该函数被称为是高阶函数。**

例如：

```kotlin
private fun highFuc(str: String, block: (String) -> Unit) {
    block(str)
}
```

其中 highFuc 是函数名，函数中传入了 2 个参数，第一个参数为 String 类型，第二个参数是 Function 类型，**->** 左边的部分用来声明该函数接收什么参数的，多个参数之间用逗号隔开，如果没有参数直接使用 **()** 表示就可以了；**->** 右边表示该函数的返回值是什么类型，如果没有返回值直接使用 Unit 即可。

## 二、内联函数

内联函数，就是**在编译时将作为函数参数的 Function 直接映射到调用处。**

例如：

```kotlin
fun invokeSay() {
    sayTest()
}

fun sayTest() {
    println("test")
}
```

sayTest() 中打印了一个字符串，然后 invokeSay() 中调用了 sayTest()，将上述代码转换成java代码之后：

```java
public final void invokeSay() {
   this.sayTest();
}

public final void sayTest() {
   String var1 = "test";
   System.out.println(var1);
}
```

然后在 sayTest() 前面加上 inline：

```kotlin
fun invokeSay() {
    sayTest()
}

inline fun sayTest() {
    println("inline fun")
}
```

转换成java之后：

```java
public final void invokeSay() {
   String var1 = "inline fun";
   System.out.println(var1);
}
```

可以看到转换成 java 后的代码有明显的区别：加上 inline 后， sayTest() 中的内容直接 “复制粘贴” 到 invokeSay() 中了，即内联到函数调用处了。

通过上面的例子，inline的作用就很明显了，就是在编译时直接将函数内容直接复制粘贴到调用处。

我们知道函数调用最终是通过JVM操作数栈的栈帧完成的，每一个方法从调用开始至执行完成的过程，都对应着一个栈帧在虚拟机栈里从入栈到出栈的过程，使用了 inline 关键字理论上可以减少一个栈帧层级。

**那么是不是所有的函数前面都适合加上inline关键字了呢？**

答案是否定的，其实JVM本身在编译时，就支持函数内联，并不是 kotlin 中特有的，那么 kotlin 中什么样的函数才需要使用 inline 关键字呢？答：高阶函数。

只有高阶函数中才需要 inline 去做内联优化，普通函数并不需要，如果在普通函数强行加上 inline，编辑器会立刻提醒：

	Expected performance impact from inlining is insignificant. Inlining works best for functions with parameters of functional types

意思是：**内联对普通函数性能优化的预期影响是微不足道的。内联最适合带有函数类型参数的函数。**

**为什么高阶函数要使用 inline ？inline 优化了什么问题呢？**

因为当函数的最后一个参数是函数类型时，我们通常使用 Lambda 表达式的形式来书写，而 Lambda 表达式在编译转换后被换成了匿名类的实现方式。

```kotlin
fun testLambda() {
    highFuc("testLambda") { str ->
        println(str)
    }
}

fun highFuc(str: String, block: (String) -> Unit) {
    block(str)
}
```

转换成 java 后：

```java
public final void testLambda() {
   this.highFuc("testLambda", (Function1)null.INSTANCE);
}

private final void highFuc(String name, Function1 block) {
   block.invoke(name);
}

public interface Function1<in P1, out R> : Function<R> {
    public operator fun invoke(p1: P1): R
}
```

highFuc 的 block 参数最终会转换成interface，并通过创建一个匿名实例来实现。这样就会造成额外的内存开销。为了解决这个问题，kotlin 引入 inline 内联功能，将 Lambda 表达式带来的性能开销消除。

下面我们给 highFuc 加上 inline 关键字：

```kotlin
fun testInline() {
    highFuc("inline") { str ->
        println(str)
    }
}

inline fun highFuc(str: String, block: (String) -> Unit) {
    block(str)
}
```

转换成 java 之后：

```java
public final void testInline() {
   String str$iv = "inline";
   System.out.println(str$iv);
}
```

可见，使用 inline 之后减少了内部类的创建。

## 三、noinline

当函数被 inline 标记时，使用 noinline 可以使函数参数不被内联。

```kotlin
fun testNoinline() {
    highFuc({
        println("noinline")
    }, {
        println("inline")
    })
}

// highFuc 被 inline 修饰，而函数参数 block0() 使用了 noinline 修饰
inline fun highFuc(noinline block0: () -> Unit, block1: () -> Unit) {
    block0()
    block1()
}
```

转换成java之后：

```java
public final void testNoinline() {
   Function0 block0$iv = (Function0)null.INSTANCE;
   block0$iv.invoke();
   
   String var1 = "inline";
   System.out.println(var1);
}
```

可以看到，block0() 函数没有被内联，而 block1() 函数被内联，这就是 noinline 的作用。

**那什么场景下使用 noinline 呢？为什么 inline 之后又需要 noinline ？**

举个例子：

如果在非内联函数 Lambda 中直接 return 怎么办？

例如：

```kotlin
fun test() {
    highFuc {
        return // 编译器会提示：'return' is not allowed here
    }
}

fun highFuc(block: () -> Unit) {
    println("before")
    block()
    println("after")
}
```

上面的代码会直接报错，在 return 的地方报 **‘return’ is not allowed here** 。 但是可以写成 return@highFuc，即：

```kotlin
fun test() {
    highFuc {
        return@highFuc
    }
}

fun highFuc(block: () -> Unit) {
    println("before")
    block()
    println("after")
}
```

其中 return 是全局返回，会影响 Lambda 之后的执行流程；而 return@highFuc 是局部返回，不会影响Lambda之后的执行流程。如果想全局返回，那么可以通过 inline 来进行声明：

```kotlin
fun test() {
    highFuc {
        return 
    }
}

inline fun highFuc(block: () -> Unit) {
    println("before")
    block()
    println("after")
}
```

因为 highFuc 通过 inline 声明为内联函数，所以调用方可以直接使用 return 进行全局返回，执行 test() 的结果：

	before

可以看到 Lambda 之后的 after 并没有被执行，因为是全局返回。当然可以改成 return@highFuc 局部返回，这样就可以都执行了。

结论：**内联函数所引用的 Lambda 表达式中是可以使用 return 关键字来进行函数返回的，而非内联函数只能进行局部返回。**

现在还有一种场景，我既想使用 inline 优化高阶函数，同时又不想调用方打断我的执行流程(因为 inline 是支持全局 return 的。在对外提供 api 的场景中很容易碰到这种需求。)，貌似冲突了，这时候怎么办呢，这时候就需要crossinline了。

## 四、crossinline

允许 inline 内联函数里的函数类型参数可以被间接调用，但是不能在 Lambda 表达式中使用全局 return 返回。

```kotlin
fun test() {
    highFuc {
        return // 错误，虽然是内联函数，但 Lambda 中使用 crossinline 修饰，所以不允许全局返回了
    }
}

inline fun highFuc(crossinline block: () -> Unit) {
    println("before")
    block()
    println("after")
}
```

crossinline 用于保证内联函数的 Lambda表达式中一定不会使用 return 全局返回，这样就不会冲突了。当然 return@highFuc 局部返回还是可以的。

## 五、总结

- inline：编译时直接将函数内容直接复制粘贴到调用处。
- noinline：当函数被 inline 标记时，使用 noinline 可以使函数参数不被内联。
- crossinline: 允许内联函数里的函数类型参数可以被间接调用，但是不能在 Lambda 表达式中使用全局 return 返回
