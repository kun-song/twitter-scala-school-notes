# 集合 I

## 基本数据结构

### 1. `Array`

* 有序
* 元素可重复
* 可变

```Scala
val numbers = Array(1, 2, 3, 4, 5)
numbers[0] = 99  // 99, 2, 3, 4, 5
```

### 2. `List`

* 有序
* 元素可重复
* 不可变

```Scala
val xs = List(1, 2, 3)
```

### 3. `Set`

* 无序
* 元素不可重复
* 不可变

```Scala
val xs = Set(1, 2, 2, 2, 2, 2)  // Set(1, 2)
```

### 4. `Tuple`

```Scala
val hostPort = ("127.0.0.1", 80)
```

通过位置访问 `Tuple` 中的元素：

```Scala
hostPort._1
hostPort._2
```

`Tuple` 非常适合用于模式匹配：

```Scala
def display(hostPort: (String, Int)): String =
  hostPort match {
    case ("127.0.0.1", _) | ("localhost", _)   ⇒ "a local host"
    case _                                     ⇒ "other"
  }
```

创建只有两个元素的 `Tuple`，可用 `->`：

```Scala
1 -> "a"
(1, "a")
```

### 5. `Map`

`Map` 是 list of tuples：

```Scala
Map(1 -> "a", 2 -> "b")  // 1 -> "a" 即 (1, "a")
```

### 6. `Option`

`Option` 是有 1 个或 0 个值的容器，有两个子类：`Some[T]` 和 `None`。

例如 `Map.get` 返回值类型就是 `Option`，表示可能没有返回值：

```Scala
val xs = Map(1 -> "a", 2 -> "b")

xs.get(1)  // Some("a")
xs.get(3)  // None
```

怎么将结果从 `Option` 中取出来呢？

首先，可以用 `getOrElse`：

```Scala
val result = xs.get(3).getOrElse(0)  // 0
```

其次，可以用模式匹配：

```Scala
val result =
  xs.get(3) match {
    case Some(v) => v
    case None    => 0  // 默认值
  }
```

## 函数组合子

### 1. `map`

对 `List` 中的每个元素应用 `f` 函数，返回 `f(x)` 组成的新 `List`：

```Scala
val xs = List(1, 2, 3, 4)

val f = (n: Int) => n * n  // 函数
val ys = xs.map(f)  // List(1, 4, 9, 16)
```

还可以传递方法给 `map`，Scala 编译器会自动将 method 转换为 function：

```Scala
def f(n: Int): Int = n * n  // 方法
val ys = xs.map(f)
```

### 2. `foreach`

类似 `map`，但没有返回值，`foreach` 专门用于产生副作用：

```Scala
xs.foreach(println)
```

### 3. `filter`

`filter` 接受 `predicate`，从 `List` 中移除 `predicate` 为 `false` 的值：

```Scala
val xs.filter(_ 2 == 0)  // List(1, 3)
```

### 4. `zip`

`zip` 将两个 `List` 缝合为 list of pair：

```Scala
val xs = List(1, 2, 3)
val ys = List("a", "b", "c")

val zs = xs.zip(ys)  // List((1, a), (2, b), (3, c))
```

### 5. `partition`

以 `predicate` 结果为 `true` 或 `false` 分割 `List`：

```Scala
val xs = List(1, 2, 3, 4)

// odd: List[Int] = List(2, 4)
// even: List[Int] = List(1, 3)
val (odd, even) = xs.partition(_ % 2 == 0)
```

### 6. `find`

类似 `filter`，但仅返回第一个满足 `predicate` 的元素：

```Scala
val x = xs.find(_ % 2 == 0)  // Some(2)
val y = xs.find(_ > 100)     // None
```

### 7. `drop` & `dropWhile`

`drop` 删除前 `n` 个元素：

```Scala
val xs = List(1, 2, 3, 4)
val ys = xs.drop(2)  // List(3, 4)
```

`dropWhile` 删除前 x 个连续满足 `predicate` 的元素：

```Scala
xs.dropWhile(_ < 3)       // List(3, 4)
xs.dropWhile(_ % 2 == 0)  // List(1, 2, 3, 4)
xs.dropWhile(_ % 2 == 1)  // List(2, 3, 4)
```

### 8. `foldLeft`

```Scala
val xs = List(1, 2, 3, 4)

xs.foldLeft(0)(_ + _)
```

### 9. `foldRight`

与 `foldLeft` 类似，但方向相反：

```Scala
val xs = List(1, 2, 3, 4)

xs.foldRight(0)(_ + _)
```

### 10. `flatten`

对嵌套结构 **降维**：

```Scala
val xs = List(List(1, 2), List(3), List(4, 5, List(6)))

xs.flatten  // List(1, 2, 3, 4, 5, List(6))
```

### 11. `flatMap`

`flatMap` = `map` + `flatten`：

```Scala
val xs = List(List(1, 2), List(3))

xs.flatMap(m ⇒ m.map(_ * 2))  // List(2, 4, 6)
```

### 万能的 `fold`

上面介绍的所有组合子都可以用 `fold` 实现，例如 `map`：

```Scala
def map[A, B](xs: List[A], f: A ⇒ B): List[B] =
  xs.foldRight(List.empty[B])((h, l) ⇒ f(h) :: l)
```

### `Map` ?

`Map` 可视为 list of pairs，因此上面的所有组合子同样适用于 `Map`，只不过处理的元素变成了 `Pair`：

```Scala
val contacts = Map("Mike" -> 123, "Bob" -> 456, "Alice" -> 400)
```

过滤电话 400 以上的人：

```Scala
// Map(Bob -> 456, Alice -> 400)
contacts.filter(contact ⇒ contact._2 >= 400)
```

根据位置访问 `Tuple` 的元素并不优美，可以用模式匹配替代：

```Scala
// Map(Alice -> 400)
contacts.filter {
  case (name, phoneNumber) ⇒ name.contains("A") && phoneNumber >= 400
}
```

至于为什么 `filter` 可以接受 `case` 表达式，后面会讲解。