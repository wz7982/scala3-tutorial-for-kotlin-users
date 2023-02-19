# ADT与模式匹配

此部分主要介绍函数式编程的惯用手法ADT（algrebraic data type，代数数据类型）和模式匹配。

1. ADT与枚举

    ADT（代数数据类型）是一个函数式编程常用概念，一般分为和类型（sum type）和积类型（product type）。

    以kotlin代码举个例子，比如一个颜色枚举：

    ```kotlin
    enum class Color {
        Red, Green, Blue
    }
    ```

    Color合法的取值范围有三种，因为他的每个枚举项的可能取值有一种，而总体的取值范围就是把三项加起来。这种类型我们称之为和类型。

    再比如，我有这样一个类：

    ```kotlin
    data class A(val x: Bool, val y: Bool)
    ```

    那这个A的可能取值有几种？答案是四种。因为Bool的取值范围有true和false两种，A的取值范围就是2 * 2。这种类型我们称之为积类型。

    ADT就是把这两者结合起来。比如我们想设计一个规则计算某些图形的面积，想处理的图形有：圆形（给定半径）、长方形（给定长和宽）、三角形（给定底和高）。

    在scala里，可以用`sealed trait`来组织这个关系：

    ```scala
    sealed trait Shape

    case class Circle(radius: Double) extends Shape

    case class Rectangle(length: Double, width: Double) extends Shape

    case class Triangle(base: Double, height: Double) extends Shape
    ```

    这样我们就可以只处理这几个图形，后面的模式匹配部分，编译器可以检查是否处理完整了所有情况。

    `sealed trait`看起来好像是一个特殊的枚举，那我们能不能用枚举来写呢？

    答案是肯定的，在scala3里，枚举允许你构造这样的Shape：

    ```scala
    enum Shape {
        case Circle(radius: Double)
        case Rectangle(length: Double, width: Double)
        case Triangle(base: Double, height: Double)
    }
    ```

    我们通过枚举，省去了`class`和`extends`的定义。

2. 模式匹配

    kotlin也有`sealed interface`，并配合`when`表达式来处理上面的图形面积：

    ```kotlin
    fun area(shape: Shape): Double = when (shape) {
        is Circle -> 3.14 * shape.radius * shape.radius
        is Rectangle -> shape.length * shape.width
        is Triangle -> shape.base * shape.height / 2.0
    }
    ```

    虽然kotlin有smart cast的设定，可以智能提取其中的字段，但是上面的例子里，我们还是不得不一遍一遍重复shape.xxx。这非常繁琐，但是在scala里，我们无需写这种样板代码。

    还记得在面向对象部分介绍的`case class`自动定义的`unapply`方法吗？在这里就能派上用场了：

    ```scala
    def area(shape: Shape): Double = shape match {
        case Circle(r) => 3.14 * r * r
        case Rectangle(l, w) => l * w
        case Triangle(b, h) => b * h / 2.0
    }
    ```

    这就是函数式编程里常说的“模式匹配”，模式匹配允许我们在case分支中预先提取其中的属性。

    scala的模式匹配非常灵活，如果你想写类似上面kotlin的代码（不建议），也可以这样写：

    ```scala
    def area(shape: Shape): Double = shape match {
        case c: Circle => 3.14 * c.radius * c.radius
        case r: Rectangle => r.length * r.width
        case t: Triangle => t.base * t.height / 2.0
    }
    ```

    模式匹配是我们处理代码的利器，尝试多用一用，很快你就能发现它的好处。

3. 归纳类型

    `sealed trait`定义的ADT，并非都像上面的Shape那样简单，我们甚至在某个子项里，把它自身套进去。这被称为“归纳类型”或“递归类型”。

    比如我们想归纳一个一进制的自然数，分别有Zero和它的后继数Succ，我们可以这样来写：

    ```scala
    sealed trait Nat

    case object Zero extends Nat

    case class Succ(n: Nat) extends Nat
    ```

    这样的结构，就是一个递归的定义，我们可以用它表达任意自然数：

    ```scala
    val zero = Zero
    val one = Succ(Zero)
    val two = Succ(Succ(Zero))
    val three = Succ(Succ(Succ(Zero)))

    ......
    ```

    你可能觉得这样的类型没有用，但是后面的标准库部分，很快就能看到归纳类型的用处。

4. GADT（非必读）

    下面的部分可能对刚入门的用户有一些理解难度，如果你暂时觉得理解不了，也没关系，可以先跳过这部分。

    GADT比上面的ADT多了一个字母，其全称为（generalized algebraic data type，广义代数数据类型）

    那么它的广义体现在哪呢？在语法的体现上，就是`sealed trait`有泛型，并允许子类的`extends`字句中，指定返回的泛型。

    我们来写一个现实中的例子：一个对弹出操作类型安全的栈。

    首先，我们需要改造一下上面的自然数定义，为Succ添加一个泛型，把里面的值记录在类型层面上：

    ```scala
    sealed trait Nat

    case object Zero extends Nat

    case class Succ[N <: Nat](n: N) extends Nat
    ```

    注意，这个改造后的Nat仍然只是ADT而不是GADT，下面我们就利用这个Nat写一个真正的GADT：

    ```scala
    sealed trait Stack[N <: Nat]

    case object EmptyStack extends Stack[Zero.type]

    case class Push[N <: Nat](top: Int, stack: Stack[N]) extends Stack[Succ[N]]
    ```

    这里，我们定义了一个栈，并用泛型记录了它的长度（长度使用上面定义的自然数），栈有两个可能的取值，一个是空栈，一个是非空栈。

    空栈的长度记录为0（Zero）；而非空栈是一个递归的类型，由一个Int类型的栈顶，和一个栈的剩余部分组成，它的长度记录为剩余部分的长度 + 1（Succ[N]）。

    然后我们写一个对栈的弹出操作，并返回一个弹出顶部之后的栈，要求是只有非空的栈才能调用：

    ```scala
    def pop: Stack[N] = stack match {
        case Push(_, stack) => stack
    }
    ```

    注意，这个pop是一个偏函数，没有涵盖所有枚举项，一般来说，偏函数有可能触发运行期异常，但是在这个例子里，它永远没有机会触发异常。不信？我们来写一个例子验证一下。

    首先：我们定义一个长度为3的栈：

    ```scala
    val stack = Push(1, Push(2, Push(3, EmptyStack)))
    ```

    然后依次弹出里面的元素：

    ```scala
    val pop1 = stack.pop
    val pop2 = pop1.pop
    val pop3 = pop2.pop
    ```

    直到现在位置，都很正常，但是如果再对pop3调用弹出操作的时候：

    ```scala
    val pop4 = pop3.pop
    ```

    这时，会触发一个编译错误：

    value pop is not a member of Stack[Zero.type].

    也就是说，这个弹出操作是类型安全的，无法对一个空栈调用弹出操作，也更没机会触发运行时异常。

    上面的例子可以看到，合理利用GADT，可以让我们的代码更具安全性。
