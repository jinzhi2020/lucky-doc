# 里氏替换原则

我们的代码的根本的目的就是为了模拟这个现实的世界，所以代码中非常多的概念都来自于现实的世界。比如说面向对象，万事万物都为对象，就想日常生活中我们“找对象”这样的说辞一样的内涵。在来说面向对象中的继承，也是一样的道理，偶尔能听人说继承了家里的煤矿，心想挖煤哪有写代码有意思。

在面向对象的原则中，有一个原则叫作里氏替换原则，就是针对继承这个概念的。这一篇文档就来通过例子描述这个原则。

## 鸵鸟非鸟

鸵鸟是鸟，应该是一个孩子都知道的常识。那么为什么这个标题中说鸵鸟非鸟呢？因为这个“常识”在我们代码的实现中并不是那么容易的事情。有人说，这有什么难的，于是写出了如下的代码:

```php
<?php
class Bird{
    public void fly(){
        System.out.println("I can fly");
    }
}
class Ostrich extends Bird{}
```

在代码中，我们先声明一个“Bird”的鸟类，然后声明了一个名为“Ostrich”的鸵鸟类并且继承 Bird 类。那么这样的代码有没有什么问题呢？当然有，如下示例:

```php
<?php
Ostrich bird = new Ostrich();
bird.fly();
```

上面的代码的逻辑是错的，或者说是有违我们的生活常识的。鸵鸟是鸟不假，但是鸵鸟不会飞。这下子麻烦了，鸵鸟肯定是不会飞的，那么它是不是鸟呢(或者说应不应该继承 Bird 类)？

> 除了鸵鸟，实际上大概有 40 余种不会飞的鸟。


是的，这样的继承是违反了里氏替换原则的，那么什么是里氏替换原则呢？

## 里氏替换原则

里氏替换原则(Liskov Subsitution Principle),这个原则是 Liskov 在 1987 年提出的关于继承的原则。

> Inheritance should ensure that any property proved about supertype objects also holds for subtype objects。


其含义是指凡是父类出现的地方，都可以使用子类安全替换。如下示例:

```php
<?php

public void test(Bird bird) {
  bird.fly();
}
```

上面的代码创建了一个测试方法，这个方法接收一个传参，传参的类型为 Bird。如果调用方传入的是小燕子或者大雁是没有问题的，因为它们都会飞。但是如何向之前的代码示例那样，传入一个鸵鸟，那么是有违常识的，因为鸵鸟并不会飞。

所以我们说，这样类似于 Bird 这样的父类的设计是不符合里氏替换原则的，因为在`test`方法中并不能安全的替换成任意的 Bird 的子类。

## 重构代码

对于这样的违反里氏替换原则的父类设计，又该如何重构呢？有两种方法，如下：

- 使用继承重构，即创建两个父类，分别为 FlyBird(会飞的鸟)以及 NotFlyBird(不会飞的鸟)。如果是大雁，就继承会飞的鸟，如果是鸵鸟就继承不会飞的鸟。
- 使用接口重构，即会飞的鸟，比如说大雁，就实现一个 Fly 的接口，使之具有飞翔的能力。

那么这两种方式有什么区别吗？重构的时候，又如何选择呢？

关键就在于是否使用了成员变量。如果说，这个飞的动作不会产生新的成员变量，那么就使用接口。如果会产生一个新的成员变量，比如说"翅膀"，那么就使用继承。

为什么呢？因为接口是可以给多个类族使用的，如果产生了一个新的成员变量，比如说翅膀，会影响其他的类继承这个接口。比如说气球、飞机也同样实现了 Fly 这个类，但是并不拥有“翅膀”这个成员变量。使用代码描述如下:

```php
<?php
// 创建一个接口，表示飞翔的能力
interface Fly{
  public Object $wings;		// 翅膀
  public void fly();
}
// 创建了一个鸵鸟类
class Ostrich implements Fly {}  
// 创建了一个不需要翅膀的气球类
class Balloon implements Fly {}
```

所以这种时候我们应该使用继承重构，如下:

```php
<?php
// 创建一个表示飞翔能力的接口
interface Fly { public void fly(); }
// 创建一个鸟的父类
class Bird{ public Object $wings; }
// 创建一个会飞的鸟的父类
class FlyBird implements Fly {}
// 创建一个不会飞的鸟的父类
class NotFlyBird {}
// 创建一个燕子类继承会飞的鸟的父类
class Swallow extends FlyBird{}
// 创建一个鸵鸟类
class Ostrich extends NotFlyBird {
	public void run(){}
}
```

经过重构，我们就能够让我们的代码更加地贴近于实际，比如我们的燕子要飞翔,可以使用`(new Swallow)->fly()`,而我们的鸵鸟并没有飞行能力，却有着最快的奔跑速度,于是我们可以`(new Ostrich)->run()`。

## 总结

这一篇文档，我们通过“鸵鸟非鸟”的例子，讲解了里氏替换原则以及对于违反该原则的代码的两种重构方法，分别是采用继承或者接口实现。这两种方法使用的选择在于是否会产生新的成员变量。

大家可能并不能立马就理解这个里氏替换原则，但是想来应该很容易记住这个形象生动的“鸵鸟非鸟”的例子，想不明白的时候，再来看看这篇文档，想想这个例子就好了。