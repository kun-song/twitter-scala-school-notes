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

简单讲，rank-1 多态意味着在 Scala 中，有些类型概念可能由于 **too generic**（过于泛化），而编译器无法理解。

一个经常演示的例子是，假设有函数 `toList`，其类型为 `A => List[A]`：

```Scala
def toList[A](a: A): List[A] = a :: Nil
```

若想将 `toList` 泛化使用：

```Scala
def foo[A, B](f: A ⇒ List[A], b: B) = f(b)
```

`foo` 第一个参数 `f` 类型是 `A => List[A]`，按理说 `f(b)` 是合法调用，因为 **任意类型** 都可以调用 `f`，即使它是 `B` 类型的值。

但因为 Scala 是 rank-1 多态（because all type variables have to be fixed at the invocation site.），所以上述代码编译报错！

即使将 `B` 固定为具体类型，也会报 **type mismatch** 错误：

```Scala
def foo[A](f: A ⇒ List[A], b: Int) = f(b)
```

## 类型推导

静态类型被人诟病的重要理由是，静态类型需要大量语法开销，Scala 通过类型推到缓解该问题。

类型推导是函数式语言的常见特性，类型推导的经典算法是 Hindley-Milner 算法，最早由 ML 语言使用，H-M 算法属于 **全局类型推导**，Scala 的类型推导稍微有点不同，是 **局部类型推导**，但本质是一样的：infer constraints, and attempt to unify a type（推导约束，尽量统一类型）。

Scala 的类型推导是局部的，这意味着 Scala 每次仅分析一个表达式，最常见的局部类型推导与全局类型推导的区别例子是递归函数：

```Scala
def fact(n: Int) = if (n <= 1) 1 else n * fact(n - 1)
```

上述 `fact` 定义在 Scala 中会报错，而在 Haskell 中则正常，原因是局部类型推导无法推导递归定义的函数的类型，因为在 Scala 中需要明确指定返回值类型：

```Scala
def fact(n: Int): Int = if (n <= 1) 1 else n * fact(n - 1)
```

>Scala 采用局部类型推断的原因是，Scala 需要支持 OOP 中的子类型等概念，H-M 无法很好处理 OO 概念，因此使用一种折中的局部类型推导，兼容 FP 和 OOP。

>强烈推荐该 [博客](https://madusudanan.com/blog/scala-tutorials-part-2-type-inference-in-scala/)，对 Scala 类型推导讲的很好。

## 变型（Variance）





