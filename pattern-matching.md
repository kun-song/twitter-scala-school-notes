# 模式匹配 & 函数组合

* 函数组合
  + `compose`
  + `andThen`
* Currying vs Partial Application
* `PartialFunction`
  + `orElse` 组合
* `case` statement -> `PartialFunction`

## 函数组合

有如下函数：

```Scala
def plus10(n: Int): Int = n + 10
def multiply3(n: Int): Int = n * 3
```

`compose`：

```Scala
val f = plus10 _ compose multiply3

f(10)  // 40
```

* 先 `multiply3` 后 `plus10`

`andThen`：

```Scala
val g = plus10 _ andThen multiply3

g(10)  // 60
```

* 先 `plus10` 后 `multiply3`

## Currying vs Partial Application

### `case` statement 是什么？

`case` 语句类型为 `PartialFunction`，是 `Function` 的子类。

### 多个 `case` 语句的集合是什么？

多个 `case` 语句，是 `PartialFunction` 的组合。

### `orElse`

Scala 的 `case` 语句是定义 `PartialFunction` 的简洁形式，避免创建匿名类的实例，可以用 `orElse` 组合 `PartialFunction`：

```Scala
val one: PartialFunction[Int, String] = { case 1 ⇒ "one" }
val two: PartialFunction[Int, String] = { case 2 ⇒ "two" }
val three: PartialFunction[Int, String] = { case 3 ⇒ "three" }
```

组合：

```Scala
val oneTwoThree = one orElse two orElse three

oneTwoThree(1)  // one
oneTwoThree(2)  // two
oneTwoThree(3)  // three
```

### `filter` 中使用 `case` 语句

因为 `case` 语句是 `PartialFunction`，进而也是函数，所以可以用于 `filter`。








