# Kotlin 从学习到 Android 第五章 返回和跳转 #
在 Kotlin 中，有三种返回和跳转语句：

- return 从最近的一个封闭函数或匿名函数中返回；
- break 跳出最近的封闭循环；
- continue 继续执行最近封闭循环的下一步；

上面这三个表达式也能够作为其他表达式的一部分：

	val s = person.name ?: return

## break 和 continue 标签 ##
在 Kotlin 中任何表达式都可以使用标签，标签的格式为：标签名@，例如，abc@ 、m@ 等。使用标签时，我们只需把标签放在表达式前面即可，一般和表达式有一个空格的距离：

	// 再 i = 10 时跳出整个循环
	loop@ for (i in 1..100) {
        for (j in 1..10) {
            if (i == 10) break@loop else println("$i : $j")
        }
    }

## 使用标签的 return ##
一般情况下，Kotlin 中的 return 和 java 中的 return 效果一样，都是结束最近的封闭函数：

	var ints = arrayListOf<Int>(0 ,1 ,2, 3)
    fun foo() {// 并不会打印出任何值
        ints.forEach {
            if (it == 0) return
            print(it)
        }
    } 

但是，当 return 后面跟上标签时，表示符合判断条件后终止标签所标记的表达式，类似于 break 的作用：

    var ints = arrayListOf<Int>(0 ,1 ,2, 3)
    fun foo() {
        ints.forEach lit@ {
            if (it == 0) return@lit
            print(it)
        }
    }

lit@ 是后面表达式的标签，当 it == 0 时，终止表达式 {...} ，ints.forEach 继续执行，所以打印结果是 123

然而，更多情况下我们使用匿名标签，其标签名和 lambda 表达式中函数的名称一样：

	fun foo() {
	    ints.forEach {
	        if (it == 0) return@forEach
	        print(it)
	    }
	}

或者，我们也可以使用一个匿名函数来代替 lambda 表达式，这样这个 return 的作用就是终止当前的匿名函数：

	fun foo() {
	    ints.forEach(fun(value: Int) {
	        if (value == 0) return
	        print(value)
	    })
	}

当需要返回值时，我们可以这么写：

	return@a 1

这句话的意思是：表达式 @a 的返回结果是 1 ，而不是返回一个有标记的表达式 （@a 1）。