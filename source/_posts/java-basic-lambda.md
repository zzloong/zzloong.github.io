---
title: Java 基础 —— Lambda 表达式
date: 2020-07-04 12:49:10
tags:
  - Java
  - Lambda
  - 面向对象
categories:
  - Java
keywords:
  - lambda
---

## 概述

Lambda 表达式是 Java 8 的重要更新，它支持将代码块作为方法参数、允许使用更简洁的代码来创建只有一个抽象方法的接口的实例。

> 描述中提到的接口称为函数式接口

## 语法

Lambda 表达式的主要作用就是可以用于简化创建匿名内部类对象，Lambda 表达式的代码块将会用于实现抽象方法的方法体，Lambda 表达式就相当于一个匿名方法。

Lambda 表达式由三部分组成：
- 形参列表：形参列表允许省略类型，如果形参列表中只有一个参数，形参列表的圆括号也可以省略；
- 箭头（`->`）：通过英文画线和大于符号组成；
- 代码块：如果代码块只有一条语句，花括号可以省略。Lambda 代码块只有一条 return 语句，可以省略 return 关键字，Lambda 表达式会自动返回这条语句的值作为返回值。

## 示例

```java
interface Eatable {
    void taste();
}

interface Flyable {
    void fly(String weather);
}

interface Addable {
    int add(int a, int b);
}


public class LambdaQs {
    // 调用该方法需要传入一个 Eatable 类型的对象
    public void eat(Eatable e) {
        System.out.println(e);
        e.taste();
    }

    // 调用该方法需要传入 Flyable 类型的对象
    public void drive(Flyable f) {
        System.out.println("我正在驾驶：" + f);
        f.fly("「夏日晴天」");
    }

    // 调用该方法需要 Addable 类型的对象
    public void calc(Addable add) {
        System.out.println("5 + 3 = " + add.add(5, 3));
    }

    public static void main(String[] args) {
        LambdaQs lq = new LambdaQs();
        // Lambda 表达式的代码块只有一句，因此省略了花括号
        lq.eat(() -> System.out.println("雪糕的味道不错！"));
        // Lambda 表达式的形参只有一个参数，因此省略了圆括号
        lq.drive(weather -> {
            // 对接口中抽象方法 fly 的重写
            System.out.println("今天天气是：" + weather);
            System.out.println("飞机平稳飞行！");
        });
        // Lambda 表达式只有一条语句，即使该表达式需要返回值，也可以省略 return
        lq.calc((a, b) -> a + b);
        // 如果不用 Lambda 表达式，就需要如下匿名类的方式去重写抽象方法
        lq.calc(new Addable() {
            @Override
            public int add(int a, int b) {
                return a + b;
            }
        });
    }
}
```

输出结果：
```java
oop.lambda.LambdaQs$$Lambda$1/1607521710@7ef20235
雪糕的味道不错！
我正在驾驶：oop.lambda.LambdaQs$$Lambda$2/1329552164@15aeb7ab
今天天气是：「夏日晴天」
飞机平稳飞行！
5 + 3 = 8
5 + 3 = 8
```

以上示例可以说明，Lambda 表达式实际上可以被当做一个具体的对象。

 ## Lambda 表达式与函数式接口

 Lambda 表达式的类型，也被称为「目标类型（`target type`）」。**Lambda 表达式的目标类型必须是「函数式接口（`functional interface`）」**。函数式接口代表只包含一个抽象方法的接口。函数式接口可以包含多个默认方法、类方法，但仅能声明一个抽象方法。

 查询 Java 8 的 API 文档，可以发现大量的函数式接口，例如：Runnable、ActionListener 等接口都是函数式接口。

 > Java 8 专门为函数式接口提供了 `@FunctionalInterface` 注解。该注解就是用于告诉编译器校验接口必须是函数式接口，否则就报错。

由于 Lambda 表达式的结果就是被当做对象/实例，因此，可以使用 Lambda 表达式进行赋值，示例：

```java
Runnable r = () -> {
    for (int i = 0; i < 100; i++) {
        System.out.println(i);
    }
};
```

我们看一下 Runnable 接口的定义：
```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

看一个错误示例：
```java
Object obj = () -> {
    for (int i = 0; i < 100; i++) {
        System.out.println(i);
    }
};
```

上面这段代码会报错：`Target type of a lambda conversion must be an interface`。Lambda 表达式的目标类型必须是明确的函数式接口！将 Lambda 表达式赋值给 Object 类型的变量，编译器只能推断出它的表达类型为 Object，而 Object 并不是函数式接口，因此就报错了！

为了保证 Lambda 表达式的目标类型是明确的函数式接口，有如下三种常见方式：
- 将 Lambda 表达式赋值给函数式接口类型的变量；
- 将 Lambda 表达式作为函数式接口类型的参数传给某个方法；
- 使用函数式接口对 Lambda 表达式进行强制类型转换；

将上面出错的代码可以进行如下的改写：

```java
Object obj1 = (Runnable)() -> {
    for (int i = 0; i < 100; i++) {
        System.out.println(i);
    }
};
```

综上，Lambda 表达式的本质很简单，就是使用简单的语法来创建函数式接口的实例，避免匿名内部类的繁琐。

## 方法引用于构造器引用

如果 Lambda 表达式的代码块只有一条代码，还可以在代码中使用方法引用和构造器引用。

方法引用和构造器引用的好处是使 Lambda 表达式的代码块更加简洁。方法引用和构造器引用都需要使用两个英文冒号 `::`。

| 种类                   | 示例               | 说明                                                         | 对应的 Lambda 表达式                      |
| ---------------------- | ------------------ | ------------------------------------------------------------ | ----------------------------------------- |
| 引用类方法             | 类名::类方法       | 函数式接口中被实现的方法的全部参数传给该类方法作为参数       | `(a,b,...) -> 类名.类方法(a,b,...)`       |
| 引用特定对象的实例方法 | 特定对象::实例方法 | 函数式接口中被实现的方法的全部参数传给该方法作为参数         | `(a,b,...) -> 特定对象.实例方法(a,b,...)` |
| 引用某类对象的实例方法 | 类名::实例方法     | 函数式接口中被实现的方法的第一个参数作为调用者，后面的参数全部传给该方法作为参数 | `(a,b,...)->a.实例方法(b,...)`            |
| 引用构造器             | 类名::new          | 函数式接口中被实现方法的全部参数传给该构造器作为参数         | `(a,b,...)->new 类名(a,b,...)`            |

```java
@FunctionalInterface
interface Converter {
    Integer convert(String from);
}

@FunctionalInterface
interface MyTest {
    String test(String a, int b, int c);
}

@FunctionalInterface
interface YourTest {
    // 抽象方法负责根据 String 参数生成一个 JFrame 返回值
    JFrame win(String title);
}


public class LambdaRef {
    public static void main(String[] args) {
        // 1 引用类方法
        // 下面使用 Lambda 表达式创建 Converter 对象
        Converter converter1 = from -> Integer.valueOf(from);
        Integer val = converter1.convert("99");

        // 函数式接口中被实现方法的全部参数传给该类方法作为参数
        Converter converter2 = Integer::valueOf;
        Integer val2 = converter2.convert("100");

        // 2 引用特定对象的实例方法
        // 使用 Lmabda 表达式创建 Converter 对象
        Converter converter3 = from -> "hello michael翔".indexOf(from);

        // 调用 "hello michael翔"的indexOf()实例方法
        // 函数式接口中被实现的全部参数传给该方法作为参数
        Converter converter4 = "hello michael翔"::indexOf;

        // 3 引用某类对象的实例方法
        // 使用 Lambda 表达式创建 MyTest 对象
        MyTest mt = (a, b, c) -> a.substring(b, c);
        String  str = mt.test("Hello World, Hello Michael翔", 2,9);

        // 上面 Lambda 表达式只有一行，因此可以使用如下引用进行替换
        // 函数式接口中被实现方法的第一个参数作为调用者
        // 后面的参数全部传给该方法作为参数
        MyTest str2 = String::substring;

        // 4 引用构造器
        // 使用 Lambda 表达式创建 YourTest 对象
        YourTest yt = a -> new JFrame(a);
        JFrame jf = yt.win("窗口");

        // 使用构造器引用进行替换
        // 函数式接口中被实现方法的全部参数传给该构造器作为参数
        YourTest yt2 = JFrame::new;
        JFrame jf2 = yt.win("窗口2");
    }
}
```


