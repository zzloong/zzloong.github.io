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

![重庆-南山一棵树](https://gitee.com/michael_xiang/images/raw/master/uPic/CACVrU.png)

## 概述

阅读项目代码时，尤其是阅读一些源码时，经常会遇到 Lambda 表达式。对此之前看过相关文章，但是停留在模模糊糊的印象上。今天趁着有时间，通过一些 demo 示例，梳理一下它的用法，以备后期遗忘的时候快速查询它的用法！

Lambda 表达式是 Java 8 的重要更新，它支持将代码块作为方法参数、允许使用更简洁的代码来创建只有一个抽象方法的接口的实例。

> 描述中提到的接口称为函数式接口

<!-- more -->

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

## 方法引用与构造器引用

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

## Lambda 表达式与匿名内部类的联系与区别

Lambda 表达式与匿名内部类存在如下相同点：
- Lambda 表达式与匿名内部类一样，都可以直接访问 `effectively final` 的局部变量，以及外部类的成员变量（包括示例变量和类变量）；
- Lambda 表达式创建的对象与匿名内部类生成的对象一样，都可以直接调用从接口中继承的默认方法；

Lambda 表达式与匿名内部类的区别：
- 匿名内部类可以为任意接口创建实例，不管接口包含多少个抽象方法，只要匿名内部类实现所有抽象方法即可；但是 Lambda 表达式只能为函数式接口创建实例；
- 匿名内部类可以为抽象类甚至普通类创建实例，但是 Lambda 表达式只能为函数式接口创建实例；
- 匿名内部类实现的抽象方法体允许调用接口中定义的默认方法，但是 Lambda 表达式的代码块不允许调用接口中定义的默认方法；

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

## Lambda 表达式调用 Arrays 的类方法

Arrays 类的有些方法需要 Comparator、XxxOperator、XxxFunction 等接口的实例，这些接口都是函数式接口。因此，可以使用 Lambda 表达式来调用 Arrays 的方法。

```java
public class LambdaArrays {
    public static void main(String[] args) {
        String[] arr1 = new String[]{"java", "python", "rust", "go"};
        Arrays.parallelSort(arr1, (o1, o2) -> o1.length() - o2.length());
        System.out.println(Arrays.toString(arr1));
        int[] arr2 = {3, -4, 25, 16, 30, 18};
        // left 代表数组中前一个索引处的元素，计算第一个元素时，left 为 1；
        // right 代表数组中的当前索引处的元素
        Arrays.parallelPrefix(arr2, (left, right) -> left * right);
        System.out.println(Arrays.toString(arr2));
        long[] arr3 = new long[5];
        // a 代表正在计算的元素索引
        Arrays.parallelSetAll(arr3, a -> a * 5);
        System.out.println(Arrays.toString(arr3));
        
        // 等价于用匿名内部类重写 applyAsLong 抽象方法
        Arrays.parallelSetAll(arr3, new IntToLongFunction() {
            @Override
            public long applyAsLong(int value) {
                return value * 5;
            }
        });
        System.out.println(Arrays.toString(arr3));
    }
}
```

输出：
```java
[go, java, rust, python]
[3, -12, -300, -4800, -144000, -2592000]
[0, 5, 10, 15, 20]
[0, 5, 10, 15, 20]
```

因为这些要出入 Comparator、XxxOperator、XxxFunction 等接口的实例往往都是一次性的，使用 Lambda 表达式也不用考虑重用等，反而让程序更加简洁了。

## 总结

本文主要参考的是 《疯狂 Java 讲义第 5 版》的第 6 章的面向对象下，通过实际的示例 demo 应该可以将 Lambda 的常用场景和用法掌握了。这样，看项目代码或者源码的话，会更加易于理解！基本功扎实，才能走得更快！

## 参考

- [To Be Top Javaer/糖块十二、Lambda表达式](http://hollischuang.gitee.io/tobetopjavaer/#/basics/java-basic/syntactic-sugar?id=%e7%b3%96%e5%9d%97%e5%8d%81%e4%ba%8c%e3%80%81lambda%e8%a1%a8%e8%be%be%e5%bc%8f)

