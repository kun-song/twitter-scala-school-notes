# 基础 I

## 为何选择 Scala ？

* 表达能力
  + 函数是第一等公民
  + 闭包
* 简洁
  + 类型推断
  + literal syntax for function creation（函数字面值？）
* Java 互操作性
  + 重用 Java 类库
  + 重用 Java 工具
  + 无性能惩罚

## Scala 工作原理

1. 编译成 Java 字节码
2. 运行在 JVM 上
  * Scala 编译器作者同样也是 Java 编译器的作者（一个人吊打一个团队）

## 表达式

Scala 中，几乎一切都是表达式：

```Scala
// res0: Int = 2
1 + 1
```

`res0` 是为表达式计算结果自动生成的 **名字**。

## 值

可以给 **表达式的计算结果** 指定名字：

```Scala
val two = 1 + 1
```

* 将名字 `two` 与表达式 `1 + 1` 的结果绑定
* 不能修改 `val` 定义的绑定

## 变量

若需要修改绑定，可用 `var`：

```Scala
val name = "Mike"

name = "Bob"
```

## 函数

使用 `def` 定义函数：

```Scala
// addOne: addOne[](val n: Int) => Int
def addOne(n: Int): Int = n + 1
```

若 **不需要参数**，则可以省略 `()`，如

```Scala
// three: three[]() => Int
def three() = 1 + 2
```

可以改为：

```Scala
// three: three[] => Int
def three = 1 + 2
```

调用时可以省略 `()`：

```Scala
three()
three
```

## 匿名函数

创建匿名函数：

```Scala
// res2: Int => Int = com.satansk.akka.A$A11$A$A11$$Lambda$1169/981061329@6a7083fa
(n: Int) => n + 1
```

解释器自动将其绑定到 `res2` 上，可以像 `addOne` 一样使用 `res2`：

```Scala
res2(10)  // 11
```

也可将匿名函数绑定到给定名字：

```Scala
// addOne: Int => Int = com.satansk.akka.A$A15$A$A15$$Lambda$1204/962231216@2d21d3b
val addOne = (x: Int) => x + 1
```

## 部分应用

应用：将函数应用到参数上。

部分应用，即使用 `_` 作为通配符调用函数，结果为另一个函数，若有：

```Scala
// (Int, Int) => Int
def add(m: Int, n: Int): Int = m + n
```

部分应用第二个参数：

```Scala
// Int => Int
val add10 = add(10, _: Int)
```

`add10` 将第一个参数固定为 `10`，可以像普通函数一样调用 `add10`：

```Scala
add10(1)  // 11
```

当然，并非只能部分应用最后一个参数，**任何参数** 都可以部分应用。

## 柯里化

有时能够分多次指定函数的参数很有用：

```Scala
def multiply(x: Int)(y: Int): Int = x * y
```

可以用两个参数调用 `multiply`：

```Scala
multiply(2)(3)
```

也可以固定第一个参数，然后部分应用第二个参数：

```Scala
// Int => Int
val multiply10 = multiply(10) _
```

可以像普通函数一样使用 `multiply10`：

```Scala
multiply10(9)  // 90
```

实际上，可以将 **任意多参函数** 柯里化：

```Scala
def add(m: Int, n: Int): Int = m + n

// Int => Int => Int
val curriedAdd = (add _).curried
```

`curriedAdd` 是一个多参数，类似 `multiply`：

```Scala
// Int => Int
val add10 = curriedAdd(10)

add10(1)  // 11
```

## 变长参数

使用 `*` 表示变长参数：

```Scala
def addAll(xs: Int*): Int = xs.sum

addAll(1, 2, 3)  // 6
```

## 类

与 Java 类似，但不需要定义 getter/setter：

```Scala
class Phone {
  val brand: String = "Apple"
  def add(x: Int, y: Int): Int = x + y
}
```

因为 `Phone` 构造函数无参数，因此构造时不需要 `()`：

```Scala
val phone = new Phone
```

可以调用 `phone` 对象上的 `add` 方法：

```Scala
phone.add(1, 10)  // 11
```

可以直接访问 `brand` 字段：

```Scala
phone.brand  // Apple
```

## 构造函数

与 Java 不同，Scala 类的构造函数并非单独方法，而是与类定义混合在一起：

```Scala
class Phone(brand: String) {
  val color: String = if (brand == "Apple") "white" else "black"
  def add(x: Int, y: Int): Int = x + y
}
```

* `Phone` 现在有两个字段：`brand` 和 `color`；

使用上与 Java 类似：

```Scala
val p = new Phone("Apple")

p.color  // while
```

## 函数 vs 方法

Scala 函数与方法的区别非常微妙，它们在大多数场景下是可以互换的，但也有不可以的时候：

```Scala
class Counter {
  var count = 0

  def methodIncr = count += 1
  def functionIncr = () ⇒ count += 1
}
```

其中 `methodIncr` 是方法，`functionIncr` 是函数，调用时：

```Scala
val c = new Counter
c.methodIncr
c.count  // 1

c.functionIncr
c.count  // 1
```

`c.functionIncr` 返回的是 **函数字面值**（匿名函数），若要调用 `functionIncr`，需要加 `()`：

```Scala
c.functionIncr()
c.count  // 2
```

## 继承

```Scala
class SmartPhone(brand: String) extends Phone(brand) {
  def log = println(s"brand = $brand, color = $color")
}
```

使用如下：

```Scala
val sp = new SmartPhone("Huawei")
sp.log  // brand = Huawei, color = black
```

## 特质

特质是 fields + behaviors 的集合：

```Scala
trait Car {
  val brand: String
}

trait Shiny {
  val shineRefraction: Int
}

trait Drive {
  def run(speed: Int): String = s"speed = $speed"
}
```

类可以 `extends` 单个特质：

```Scala
class BMW extends Car {
  override val brand = "BMW"
}
```

也可以 `with` 多个特质：

```Scala
class BMW extends Car with Shiny with Drive {
  override val shineRefraction = 10
  override val brand = "BMW"
}

// speed = 100
(new BMW).run(100)
```

### `trait` vs 抽象类

* 优先使用 `trait`，因此可以混入任意数量的特质，但只能继承一个抽象类；
* 若需要构造参数，则只能用抽象类，`trait` 不能有构造参数；
