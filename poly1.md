# 类型 & 多态基础

## 静态类型

利用类型，可以表示函数的定义域、值域：

```Java
f: R -> N
```

函数 `f` 的类型是 `R => N`，将 real number 映射到 natural number，因此如果 `f(x)` 中的 `x` 不是 real number，`f(x)` 无法编译通过。

通过 **类型标记**，编译器可以静态地（即在编译时）检查程序是否符合 **类型约束**，但 type checker：

* 只能保证 **不满足** 约束的程序无法通过检查
* 不能保证 **满足** 约束的程序一定能通过检查

静态类型让我们在编译时发现更多错误，从而减少线上问题，而类型系统越强大，编译时发现的问题就越多，程序就越可靠，所以学术界一直在增强类型系统的 **表现力**，而 Scala 的类型系统是非常非常强大的。

>**注意**：所有类型信息在编译完成后都会被移除，因为编译后不再需要类型信息，这称为 **类型擦除**。

## Scala 类型系统

Scala 类型系统的表现力非常强，主要特性有：

* 参数多态（parametric polymorphism，也可以粗略认为是 generic）
* （局部）类型推断
* existential quantification（存在量化）
  + roughly, defining something for some **unnamed** type
* 视图（views）
  + 粗略讲，即将类型 `A` 的值 **转换** 为类型 `B` 的值

### 1. 类型多态

利用多态，可以在 **不失去** 静态类型表现力的前提下，编写泛型代码（即可用于不同类型值的代码）。

我们知道动态类型的 type check 发生在运行时，因此编译时无法获取变量的类型，所以写代码约束很少，非常灵活；而静态类型的 type check 发生在编译时，因此我们希望在写代码时能尽量多利用类型系统，来帮我们做更多 type check。

如果没有类型多态，编写泛型代码类似：

```Scala
scala> 2 :: 1 :: "bar" :: "foo" :: Nil
res0: List[Any] = List(2, 1, bar, foo)
```

编译器不会报错，但是无法获取 `res0` 中元素的类型了，从而使用 `res0[0]` 时就没有 type checker 帮我们做检查了：

```Scala
scala> res0.head
res1: Any = 2
```

因此程序 **退化** 为一系列的类型转换（`asInstanceOf`），从而失去了类型系统的表现力。

而类型多态解决了该问题，通过指定 **类型变量** 即可实现类型多态：

```Scala
scala> def drop1[A](l: List[A]) = l.tail
drop1: [A](l: List[A])List[A]

scala> drop1(List(1,2,3))
res1: List[Int] = List(2, 3)
```

### Scala 的多态是 rank-1 多态（秩 1 多态）





