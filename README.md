# 写给kotlin用户的scala3教程

## Scala3简介

Scala是洛桑联邦理工学院教授马丁奥德斯基主导开发的基于JVM的编程语言。马丁教授是编程语言方面的专家，致力于编程语言理论的研究。

1996年，在Java诞生一年后，马丁教授与他人合作编写了一个名为Pizza的Java方言，将函数式编程中的泛型、高阶函数、模式匹配等特性移植到JVM平台。后来Pizza语言被Sun公司看中，于是邀请马丁为Java加入泛型支持。马丁开展了名为Generic-Java的项目工作，这也是后来Java5的前身，并且，Generic-Java的编译器也成为标准的Java编译器javac。

其后，马丁教授逐渐感受到，Java是一门有着十分多限制的语言，继续在Java上做增强实现不了他的诸多想法，于是在2003年，他决定另起炉灶，在JVM上设计一门新的编程语言，Scala就此诞生。

Scala得名于scalable（可扩展的），与Java8对函数式编程的浅尝辄止不同，Scala将面向对象编程和函数式编程深度结合，带来了强大的表达力和扩展性。并在大数据、芯片设计等领域大放异彩。

近年来，马丁及其团队对Scala的类型系统进行了整理规范，并将其命名为Dependent Object Type（依赖对象类型），简称DOT，并基于DOT设计了一种名为Dotty的新编程语言，它兼容以前的Scala，而Dotty还有另一个名字：Scala3。这也正是本教程的主角。

## Scala与Kotlin的对比

同为JVM上的编程语言，并且Scala诞生时间早于Kotlin，Kotlin的设计极大程度受到了Scala的影响，比如使用`val`和`var`区分变量可变性；`object`单例对象；以及最初的Kotlin版本中，接口使用关键字`trait`而不是`interface`等等。

但是两者又有很大的不同：

首先是类型系统，Kotlin的类型系统可以认为是Java类型系统的延续，并没有进行大刀阔斧的修改，只是在工程方面进行了一些安全性和易用性的改进，比如Kotlin在类型层面区分可空性，使用形如`Int`和`Int?`的写法进行区分；而Scala3的类型系统是十分强大的，Kotlin的这种“可空类型”，只不过是Scala3并集类型的一种使用场景，写作`Int | Null`，并集类型是比可空类型更通用的类型概念，并且Scala3还有很多强大的类型级别的手段，可以进行类型层面的编程，这是Kotlin力有未逮之处。

其次是抽象能力，Kotlin对Java而言，增加了一些抽象能力，比如“扩展函数”，`JvmName`注解，其可以增强语言的“特设多态”能力，而Scala3有着更丰富的“上下文抽象”手段。Scala3还提供了“宏”和其他编译期计算能力，我们可以编写程序在编译期处理Scala代码本身，相比Kotlin的反射，是一种更强大的“元编程”手段。

还有函数式编程能力，Kotlin对函数式编程有一些简单的支持，比如支持不可变变量以及函数是一等构造等。Scala3对于函数式编程有更为完善的支持，比如提供了“模式匹配”，“柯里化”，“函数复合”，“for表达式”等功能，因此Scala3可以作为一个完全的函数式编程语言来使用。

再者是语法的可能性，Kotlin有很强的“dsl”构造能力，比如它提供了“中缀函数”、“尾闭包”，“带有接收者的lambda表达式”等等特性，第三方库可以用这些特性，来创造一些新的“内嵌编程语言”。实际上这些思想也是从Scala借鉴而来的，而Scala本身有比Kotlin更强的内嵌编程语言能力，比如说Kotlin的中缀函数要求函数参数有且仅有一个，但是Scala没有这样的限制，中缀函数的参数可以是可变参数列表，也可以是多参数构成的元组；而尾闭包这个特性，在Scala中也只是柯里化的一种使用场景。Scala的语法比Kotlin有更强的通用性，语法的组合能力也更强。

不过Kotlin相比Scala3也并非完全一无是处，Kotlin虽然语言层面的能力较弱，但是其好处是与Java的亲和力极强，Java代码和Kotlin代码可以无缝互相调用，而Scala3虽然可以方便地调用Java代码，但是如果要用Java代码调用Scala3代码，恐怕体验就不会太好。

而且Kotlin的开发商是JetBrains，其是世界上第一流的IDE厂商之一，Kotlin也理所当然地有着强大的IDE支持，而Scala3目前的IDE支持与Kotlin有比较大的差距。

综上所述，Scala3有着更强大的框架开发能力，而Kotlin更倾向于业务代码的开发。

如果你有开发框架、或者创造新编程语言的打算，那么学习Scala3一定会让你有很大的收获。Kotlin在语法上实际可以看作“Scala3--”，因此如果熟悉Kotlin，在学习Scala3的时候可以获得事半功倍的效果。

本教程预设读者熟悉Kotlin，Kotlin与Scala3相似的地方，会一笔带过，着重讲述两者不同的地方。如果你并无Kotlin基础，也想学习Scala3，可以参考其他相关教程或书籍。

## 目录

[1. 变量与函数](https://github.com/wz7982/scala3-tutorial-for-kotlin-users/blob/main/%E5%8F%98%E9%87%8F%E4%B8%8E%E5%87%BD%E6%95%B0.md)

[2. 表达式](https://github.com/wz7982/scala3-tutorial-for-kotlin-users/blob/main/%E8%A1%A8%E8%BE%BE%E5%BC%8F.md)

[3. 面向对象](https://github.com/wz7982/scala3-tutorial-for-kotlin-users/blob/main/%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1.md)

[4. ADT与模式匹配](https://github.com/wz7982/scala3-tutorial-for-kotlin-users/blob/main/ADT%E4%B8%8E%E6%A8%A1%E5%BC%8F%E5%8C%B9%E9%85%8D.md)

[5. 标准库](https://github.com/wz7982/scala3-tutorial-for-kotlin-users/blob/main/%E6%A0%87%E5%87%86%E5%BA%93.md)

[6. 上下文抽象](https://github.com/wz7982/scala3-tutorial-for-kotlin-users/blob/main/%E4%B8%8A%E4%B8%8B%E6%96%87%E6%8A%BD%E8%B1%A1.md)

[7. 类型](https://github.com/wz7982/scala3-tutorial-for-kotlin-users/blob/main/%E7%B1%BB%E5%9E%8B.md)

[8. 元编程](https://github.com/wz7982/scala3-tutorial-for-kotlin-users/blob/main/%E5%85%83%E7%BC%96%E7%A8%8B.md)