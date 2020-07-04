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
 
 ## 