# Kotlin 从学习到 Android 第四章 控制流 #
## if 表达式 ##
在 Kotlin 中，if 是表达式语句，它可以返回一个值，因此在 Kotlin 中没有三目运算符（condition ? then : else）。

	// 传统的 if 用法
	var max = a 
	if (a < b) max = b
	
	// 传统的 if 和 else 用法 
	var max: Int
	if (a > b) {
	    max = a
	} else {
	    max = b
	}
	 
	// 在 Kotlin 中作为表达式的用法
	val max = if (a > b) a else b

if 表达式的分支也可以是代码块，其最后一个表达式是这个分支代码块的返回值。

	var a = 3
    var b = 5
    val max = if (a > b) {
        print("Choose a ")
        a
    } else {
        print("Choose b ")
        b
    }
    println(max)        // 5
    val min = if (a < b){
        a
        print("Choose a")
    } else {
        b
        print("Choose b")
    }
    println(min)        // kotlin.Unit

**注意**：如果你用 if 作为一个表达式使用而不是作为条件语句，那么 if 表达式必须有 else 分支。

## when 表达式 ##
when 表达式取代 switch 操作，它的一般形式为：

	when (x) {
	    1 -> print("x == 1")
	    2 -> print("x == 2")
	    else -> { // 注意这里的 else
	        print("x is neither 1 nor 2")
	    }
	}

when 按顺序逐个匹配每一个逻辑分支，知道成功匹配。when 既可以用作表达式也可以用作条件语句。当 when 用作表达式时，那个成功匹配的分支的值就是整个 when 表达式的值；当 when 作为条件语句时，分支的值将被自动忽略。（和 if 一样，when 的分支也可以是代码块，其最后一个表达式是这个分支代码块的返回值。）

当所有分支的条件都不符合时，when 就会匹配 else 分支。如果 when 作为一个表达式使用时，必须含有 else 分支，除非 **编译器** 能验证其它分支能够完全覆盖所有可能出现的匹配条件，即除 else 分支外必有一个分支被成功匹配，此时可以省略 else 分支，实际上编译器不会验证，如下面代码在 intellij 中仍然编译不通过，虽然确定不会走 else 分支，但必须有 else 。

    val a = 1
    var b: Int
    b = when (a) {
        0 -> 0
        1 -> 1
        2 -> 2
        3 -> 3
	    // else -> 3000
    }
    println(b)

如果很多条件的处理方式一样，那么这些条件可以写在一起，用逗号分割即可：

	when (x) {
	    0, 1 -> print("x == 0 or x == 1")
	    else -> print("otherwise")
	}

不仅常量可以做为分支条件，表达式也可以作为分支条件：

	when (x) {
	    parseInt(s) -> print("s encodes x")
	    else -> print("s does not encode x")
	}

分支的条件也可以用 in 或者集合修饰：

	when (x) {
	    in 1..10 -> print("x is in the range")
	    in validNumbers -> print("x is valid")
	    !in 10..20 -> print("x is outside the range")
	    else -> print("none of the above")
	}

当然你也可以使用 is 修饰，并且在成功匹配的分支中不需要强制转型就可以使用条件上判断好的类型的方法。

	fun hasPrefix(x: Any) = when(x) {
	    is String -> x.startsWith("prefix") // 验证通过则说明 x 是 String 类型，不需要再强制转型就可以使用 String 的方法
	    else -> false
	}

另外，when 也可以代替 if-else if 关系链，如果不传入判断参数，分支条件仅是 Boolean 类型，当条件为 true 时，就会执行这个分支的代码块。

	when {
	    x.isOdd() -> print("x is odd")
	    x.isEven() -> print("x is even")
	    else -> print("x is funny")
	}

## for 循环 ##
for 循环的常用方式：
	
	for (item in collection) print(item)

或

	for (item: Int in ints) {
	    // ...
	}

在 for 循环中，系统会自动创建一个 iterator ，它提供了如下方法：

- iterator()
- next()
- hasNext()，返回一个Boolean 值

如果 for 循环是通过下标创建的，那么系统不会创建 iterator ，这样性能比较高。

	for (i in array.indices) {
	    print(array[i])
	}

当然，你也可以这么写：

	for ((index, value) in array.withIndex()) {
	    println("the element at $index is $value")
	}

## while 循环 ##
while 和 do..while 与一般的使用方式一样。

	while (x > 0) {
	    x--
	}
	
	do {
	    val y = retrieveData()
	} while (y != null) 