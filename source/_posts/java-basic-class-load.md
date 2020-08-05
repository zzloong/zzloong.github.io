---
title: Java 基础 —— 类加载
date:  2020-07-31 23:25:50
tags: [Java, 类]
categories:
-  Java
---

类初始化基本知识

<!-- more -->

## JVM 和类

当使用 java 命令运行 Java 程序时，会启动一个 Java 虚拟机进程。同一个 JVM 的所有线程、所有变量都处于同一个进程里，他们都使用该 JVM 进程的内存区。当系统出现如下情况时，JVM 进程将被终止。

- 程序运行到最后正常结束
- 程序使用了 `System.exit()` 或 `Runtime.getRuntime().exit()`
- 程序遇到未捕获的异常或错误
- 程序所在平台强制结束了 JVM 进程

> 两个运行的 Java 程序处于两个不同的 JVM 进程中，两个 JVM 之间并不会共享数据。

## 类的加载

当程序主动使用某个类时，如果该类还未被加载到内存中，则系统会通过加载、连接、初始化三个步骤来进行该类的初始化。这三个步骤统称为「类加载」或「类初始化」。

「类加载」指的是将类的 class 文件读入内存，并为之创建一个 `java.lang.Class` 对象。换言之，程序中使用任何类时，系统都会为之建立一个 `java.lang.Class` 对象。

> 系统中所有的类实际上也是实例，它们都是 `java.lang.Class` 的实例。

类的加载由类加载器完成，类加载器由 JVM 提供。除此之外，开发者可以通过集成 ClassLoader 基类来自定义类加载。**类加载器通常无须等到”首次使用“该类时才加载它，Java 虚拟机规范允许系统预先加载某些类。

## 类的连接

类被加载后，系统会为之生成对应的 Class 对象，接着就会进入连接阶段。连接阶段负责把类的二进制数据合并到 JRE 中。类接连分为如下三个阶段：
1. 验证：验证阶段用于检验被加载的类是否有正确的内部结构
2. 准备：类准备阶段则负责为类的**类变量**分配内存，并设置默认初始值
3. 解析：将类的二进制数据中的符号引用替换成直接引用

## 类的初始化

类初始化阶段主要就是虚拟机堆类变量进行初始化。在 Java 类中堆类变量指定初始值有两种方法：
1. 声明类变量时指定初始值
2. 使用静态初始化块为类变量指定初始值

> 如果类变量没有指定初始值，则采用默认初始值

{% note success no-icon %}

- 静态初始化块只会被执行一次（第一次加载该类时），静态初始化块先于构造器执行。
- 类初始化块和类变量所指定的初始值都是该类的初始化代码，它们的执行顺序与源程序中的排列顺序相同。

{% endnote%}

JVM 初始化一个类包含如下几个步骤：
1. 加入该类未被加载和连接，则程序先加载并连接该类
2. 假如该类的直接父类还没有被初始化，则先初始化其直接父类
3. 假如类中有初始化语句，则系统依次执行这些初始化语句

> 第 2 个步骤中，如果直接父类又有父类，会再次重复这三个步骤

实例初始化块负责对象执行初始化，而类初始化块是类相关的，系统在类初始化阶段执行，而不是在创建对象时才执行。因此，类初始化块总是比实例初始化块先执行。只有当类初始化完成之后，才可以在系统中使用这个类，包括访问类的类方法、类变量或者用这个类来创建实例。

### 栗子

Root.java:

```java
public class Root {
    static {
        int root = 1;
        System.out.println("Root 的类初始化块");
    }

    {
        System.out.println("Root 的实例初始化块");
    }

    public Root() {
        System.out.println("Root 的无参构造器");
    }
}
```

Mid.java:

```java
public class Mid extends Root {
    static {
        int mid = 2;
        System.out.println("Mid 的类初始化块");
    }

    {
        System.out.println("Mid 的实例初始化块");
    }

    public Mid() {
        System.out.println("Mid 的无参构造器");
    }

    public Mid(String msg) {
        this();
        System.out.println("Mid 的有参构造器，其参数值：" + msg);
    }
}
```

Leaf.java:

```java
public class Leaf extends Mid {
    static {
        int leaf = 3;
        System.out.println("Leaf 的类初始化块");
    }

    {
        System.out.println("Leaf 的实例初始化块");
    }

    public Leaf() {
        super("初始化测试");
        System.out.println("执行Leaf的构造器");
    }
}
```

Test.java:

```java
public class Test {
    public static void main(String[] args) {
        new Leaf();
        new Leaf();
    }
}
```

运行结果会是怎样的呢？停下来想一想。

输出结果：
```
Root 的类初始化块
Mid 的类初始化块
Leaf 的类初始化块
Root 的实例初始化块
Root 的无参构造器
Mid 的实例初始化块
Mid 的有参构造器，其参数值：初始化测试
Leaf 的实例初始化块
执行Leaf的构造器
Root 的实例初始化块
Root 的无参构造器
Mid 的实例初始化块
Mid 的有参构造器，其参数值：初始化测试
Leaf 的实例初始化块
执行Leaf的构造器
```

说明：
- 优先进行类初始化块（类静态块）的初始化，如果有父类，那么就先进行父类的的类初始化块运行；
- 类初始化块执行完成之后，会进行示例初始化块和构造器，如果有父类，则也需要先进行父类的实例初始化块、构造器执行；

> 实例初始化块就是指没有 static 修饰的初始化块。当创建该类的 Java 对象时，系统总是先调用该类定义的实例初始化块（当然，类初始化要已经先完成）。实例初始化是在创建 Java 对象时隐式执行的，而且，**在构造器执行之前自动执行**。

实例初始化栗子

```java
public class InstanceTest {
    {
        a = 1;
    }

    int a = 2;

    public static void main(String[] args) {
        // 输出 2
        System.out.println(new InstanceTest().a);
    }
}
```

如果上面例子，将实例初始化块和实例变量声明顺序调换，输出就会变为 1

{% note success no-icon %}

创建 Java 对象时，系统纤维该对象的所有实例变量分配内存（前提是该类已被加载过），接着程序对这些实例变量进行初始化：先执行实例初始化块或声明实例变量时指定的初始值（按照它们在源码中的先后顺序赋值），然后再执行构造器里指定的初始值。

实际上实例初始化块是一个假象，使用 `javac` 命令编译 Java 类后，该 Java 类中的实例初始化块会消失—实例初始化块中代码会被“还原”到每个构造器中，且位于构造器所有代码的前面。

{% endnote%}