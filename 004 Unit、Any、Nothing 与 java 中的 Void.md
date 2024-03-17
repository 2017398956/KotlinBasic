# Unit、Any、Nothing 与 java 中的 Void

## 1.Unit

首先，看看 Unit 在Kotlin中的定义：

```kotlin
package kotlin

/**
 * The type with only one value: the `Unit` object. This type corresponds to the `void` type in Java.
 */
public object Unit {
    override fun toString() = "kotlin.Unit"
}
```

可以看到，首先 Unit 本身是一个用 object 表示的单例，所以可以理解为 Kotlin 有一个类，这个类只有一个单例对象，叫 Unit 。这个类型类似于Java中的 Void 。由于在 Kotlin 中，一切方法/函数都是表达式，表达式是总是有值的，所以每一个方法都必有一个返回值。如果没有用 return 明确的指定，那么一般来说就会用自动帮我们加上 Unit，等同于这样:

```kotlin
fun returnUnit():Unit{
    return Unit
}
```

返回值类型为Unit，实际返回了Unit的单例对象Unit。

## 2.Any

Any 在 kotlin 中的定义：

```kotlin
package kotlin

/**
 * The root of the Kotlin class hierarchy. Every Kotlin class has [Any] as a superclass.
 */
public open class Any {
    /**
     * Indicates whether some other object is "equal to" this one. Implementations must fulfil the following
     * requirements:
     *
     * * Reflexive: for any non-null value `x`, `x.equals(x)` should return true.
     * * Symmetric: for any non-null values `x` and `y`, `x.equals(y)` should return true if and only if `y.equals(x)` returns true.
     * * Transitive:  for any non-null values `x`, `y`, and `z`, if `x.equals(y)` returns true and `y.equals(z)` returns true, then `x.equals(z)` should return true.
     * * Consistent:  for any non-null values `x` and `y`, multiple invocations of `x.equals(y)` consistently return true or consistently return false, provided no information used in `equals` comparisons on the objects is modified.
     * * Never equal to null: for any non-null value `x`, `x.equals(null)` should return false.
     *
     * Read more about [equality](https://kotlinlang.org/docs/reference/equality.html) in Kotlin.
     */
    public open operator fun equals(other: Any?): Boolean

    /**
     * Returns a hash code value for the object.  The general contract of `hashCode` is:
     *
     * * Whenever it is invoked on the same object more than once, the `hashCode` method must consistently return the same integer, provided no information used in `equals` comparisons on the object is modified.
     * * If two objects are equal according to the `equals()` method, then calling the `hashCode` method on each of the two objects must produce the same integer result.
     */
    public open fun hashCode(): Int

    /**
     * Returns a string representation of the object.
     */
    public open fun toString(): String
}

```

根据注释，Any 其实就跟 Java 里的 Object 是一样的，也就是说在 Kotlin 中 Any 取代了 Java 中的 Object，成为了 Kotlin 中所有类的父类。不过这个说法还不是很严谨，因为 Any 是不可为空的，而 Kotlin 中还有一个 Any?，是指可空的 Any 。显而易见， Any? 是 Any 的父类，那么严格来说， Any? 是所有类的父类， Any 只是所有不可为空的类（也就是没有?）的父类。

## 3.Nothing

Nothing 是最难理解的一个概念，不同于 Unit 之于 Void ，Any 之于 Object ， Nothing 在Java中并没有一个相似的概念。 Nothing 的定义如下：

```kotlin
package kotlin

/**
 * Nothing has no instances. You can use Nothing to represent "a value that never exists": for example,
 * if a function has the return type of Nothing, it means that it never returns (always throws an exception).
 */
public class Nothing private constructor()
```

Nothing 是一个类，这个类构造器是私有的，也就是说我们从外面是无法构造一个 Nothing 对象的。前面说每一个方法都有返回值，且返回值至少也是一个 Unit，这是对正常方法来说的。如果一个方法的返回类型定义为 Nothing ，那么这个方法就是无法正常返回的。可以这么理解，Koltin中一切方法都是表达式，也就是都有返回值，那么正常方法返回 Unit ，无法正常返回的方法就返回 Nothing 。举个例子，定义如下两个方法：

```kotlin
fun test1():Nothing{
    while (true){}
}

fun test2():Nothing{
    while (false){}
}
```

test1() 方法是可以编译通过的，但是执行的时候程序会一直陷入循环中无法正常返回；test2() 乍一看执行的时候可以正常返回，但是因为返回类型定义为了 Nothing ，这句矛盾了，都说好了不能正常返回（定义返回类型为 Nothing ），结果你方法内部逻辑没问题，给我返回了，那就有问题了，所以他其实是编译不通过的，其实也很好理解：我需要返回一个 Nothing 类的对象，但是因为 Nothing 类不是 open 的，且构造器为私有的，所以没法返回一个他的对象。怎么让他编译通过呢？可以抛一个异常：

```kotlin
fun test2():Nothing{
    while (false){}
    throw Exception()
}
```

好了，现在我因为抛了一个异常，所以还是没法正常返回，那么我声明返回类型为 Nothing 也就没问题了。类似的用法，比如Kotlin中有一个方法 TODO ：

```kotlin
fun test3(){
    TODO()
}

@kotlin.internal.InlineOnly
public inline fun TODO(): Nothing = throw NotImplementedError()
```

因为要抛异常，注定无法正常返回，所以返回 Nothing 类型。而 test3() 方法返回值类型应该是默认的 Unit ，或者其他自己定义的返回类型，但实际上因为调用了 TODO() ，所以返回了一个 Nothing （并没有正常返回），这样编译是可以通过的，为什么呢？有一些文章是把 Nothing 当做其他所有类的子类来理解的，也就是说 Nothing 是其他类的子类，所以返回其他类的地方可以用一个返回 Nothing 来代替，这样理解也是没问题的。但是就有疑问了，Kotlin&Java不是单继承的吗？如果 Nothing 是所有类的子类，那不就是多继承了？其实这里有一个概念，我也是最近在 《Kotlin核心编程》中学到。其实“继承”和“子类化”是两个概念，我们说类A继承了类B，即 A extends B，是强调B中的一些实现，可以在A中复用；而说类A是类B的子类，是强调在需要类B的地方可以用类A代替。两者的角度不一样。单继承，指的是从继承的角度上，一切类的顶级父类都是 Any (Java中是 Object )，那么大家都可以复用父类的现有实现。而子类化，则是在所有需要用到父类的地方可以代替，比如一个方法我需要返回一个 Any，那么我实际上返回一个 Int 对象，也是没问题的。所以说，Nothing 可以代替原方法的返回类型，是从子类化的角度，因为可代替，所以 Nothing 是所有类的子类，这和单继承是没冲突的。参照这个说法，Kotlin中还有一个 emptyList :

```kotlin
/**
 * Returns an empty read-only list.  The returned list is serializable (JVM).
 * @sample samples.collections.Collections.Lists.emptyReadOnlyList
 */
public fun <T> emptyList(): List<T> = EmptyList

internal object EmptyList : List<Nothing>...
```

因为 Nothing 是其他类型的子类，所以按照泛型的协变，emptyList 就可以代替需要 List<T> 的地方了。