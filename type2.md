# 类型 II

## View bounds or type class

视图边界已经 [被废弃](https://github.com/scala/scala/pull/2909)

## Context bounds & implicitly

Scala 2.8 引入一种串联、获取 `implicit` 参数简单方式：

```Scala
def foo[A : Ordered] = ???
```

以上定义等价于：

```Scala
def foo[A](implicit x: Ordered[A]) {} = ???
```

老办法中可以用 `x` 获取 `implicit` 值，使用 context bound 则使用 `implicitly` 函数获取：

```Scala
def foo[A](implicit x: Ordered[A]) =
  implicitly[Ordered[Int]] ...
```

联合使用 context bound 和 `implicitly`，代码更加简洁。

## 