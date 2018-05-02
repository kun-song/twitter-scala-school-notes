# 基础 II

## `apply` 方法

当类 or 对象用途很单一时，`apply` 是很好的语法糖。

可以将 `apply` 用于工厂模式：

```Scala
class Car(brand: String) {
  def color: String = if (brand == "BMW") "white" else "black"
}

object CarMaker {
  def apply(brand: String) = new Car(brand)
}

val bmw = CarMaker("BMW")
bmw.color  // white
```

可以用 `apply` 将类伪装为函数：

```Scala
class AddOne(initial: Int) {
  def apply(): Int = initial + 1
}

val addOne = new AddOne(99)
addOne()
```

其中 `addOne()` 实际是 `addOne.apply()` 的语法糖，即调用 `addOne` 对象上的 `apply` 方法。

## 单例对象

使用 `object` 定义的对象为 **单例对象**：

```Scala
object Timer {
  var count = 0

  def incr = count += 1
}
```

若 `object` 定义的对象与 `class` 定义的类 **同名**，则称该对象为伴生对象，常常把伴生对象用作工厂：

```Scala
class Car(brand: String) {
  def color: String = if (brand == "BMW") "white" else "black"
}

object Car {
  def apply(brand: String) = new Car(brand)
}
```

可以在伴生对象中定义多个 `apply` 方法，从而实现多个 **工厂方法**。

## 函数即对象（Functions are Objects）

函数是一堆 `trait` 的集合，例如接受一个入参的函数是 `Function1` 特质的实例，`Function1` 定义了 `apply` 方法，从而可以像 **函数调用** 那样 **调用对象**：

```Scala
object addOne extends Function1[Int, Int] {
  override def apply(v1: Int) = v1 + 1
}

addOne(10)  // 11
```

一共有 23 个函数特质，从 `Function0` 到 `Function22`。

`apply` 语法糖统一了 OO 和 FP：

* 类的实例可以用作函数
* 函数本质上又是类的实例

**注意**：

* 类中定义的方法，并非是 `Function*` 特质的实例，因此它们并非函数，而是方法；
* repl 中定义的方法，的确是 `Function*` 的实例，是函数；

## 模式匹配

模式匹配是 Scala 最有用的特性之一。

#### 1. 匹配值

```Scala
def int2str(n: Int): String =
  n match {
    case 1  ⇒ "one"
    case 2  ⇒ "two"
    case _  ⇒ "some other number"
  }
```

#### 2. 守卫

```Scala
def int2str(n: Int): String =
  n match {
    case i if i == 1  ⇒ "one"
    case i if i == 2  ⇒ "two"
    case _            ⇒ "some other number"
  }
```

#### 3. 匹配类型

```Scala
def exception2str(e: Throwable): String =
  e match {
    case _: RuntimeException         ⇒ "runtime exception"
    case _: IllegalArgumentException ⇒ "illegal argument exception"
    case _                           ⇒ "other exception"
  }
```

#### 4. `case` class

`case` class 设计时的使用场景就是模式匹配：

```Scala
case class Phone(brand: String, costs: Int)

def displayPhone(p: Phone): String =
  p match {
    case Phone("Apple", _)       ⇒ "wow apple!"
    case Phone(_, i) if i > 1000 ⇒ "wow expensive"
    case _                       ⇒ "ordinary"
  }
```

## 异常

与 Java 的 try-catch 唯一不同之处是 catch 参数为 `PartialFunction`，一般用 `case` 语句：

```Scala
try {
  remoteCalculatorService.add(1, 2)
} catch {
  case e: ServerIsDownException => log.error(e, "the remote calculator service is unavailable. should have kept your trusty HP.")
} finally {
  remoteCalculatorService.close()
}
```

Scala 中一切皆为表达式，try-catch 也不例外：

```Scala
val result: Int = try {
  remoteCalculatorService.add(1, 2)
} catch {
  case e: ServerIsDownException => {
    log.error(e, "the remote calculator service is unavailable. should have kept your trusty HP.")
    0
  }
} finally {
  remoteCalculatorService.close()
}
```

其中 `finally` 不是表达式的一部分，因此没有值。