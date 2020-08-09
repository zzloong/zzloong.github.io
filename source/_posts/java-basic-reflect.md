---
title: Java 基础 —— 反射
date: 2020-08-09 17:49:10
tags:
  - Java
categories:
  - Java
keywords:
  - reflect
---

![远方](https://gitee.com/michael_xiang/images/raw/master/uPic/looking-glass-918878_640.jpg)

## 通过反射查看类信息

Java 程序中的许多对象在运行时会出现两种类型：编译时类型和运行时类型。例如：`Person p = new Student();`，代码会生成一个 p 变量，编译时类型是 Person，运行时类型为 Student。除此之外，有时程序在运行时接收到外部传入的一个编译类型为 Object，但程序又需要调用该对象运行时类型的方法。

未解决这些问题，程序需要运行时发现对象和类的真实信息。有下面两种做法：
1. 先试用 `instanceOf` 运算符进行判断，再利用强制类型转换将其转换成运行时类型的变量；
2. 程序只依靠运行时信息来发现该对象和类的真实信息，这就必须使用「反射」；

本文就主要是来介绍反射知识点的。

### 获得 Class 对象

之前[文章](https://michael728.github.io/2020/08/07/java-basic-class-load/)已经介绍过类加载了，每个类被加载之后，系统会为该类生成一个对应的 Class 对象，通过该 Class 对象就可以访问 JVM 中的这个类。Java 程序中获得 Class 对象有如下三种方式：
1. 使用 Class 类的 `forName(String clazzName)` 静态方法。该方法需要传入字符串参数，该字符串参数的值是某个类的「全限定名」（必须是完成包名）。
2. 调用某个类的 class 属性来获取该类对应的 Class 对象。例如 `Person.class` 将会返回 Person 类对应的 Class 对象。
3. 调用某个实例对象的 `getClass()` 方法。该方法是 `java.lang.Object` 类中的方法，因此所有 Java 对象都可以调用该方法。

方式 1 和方式 2 都是直接根据类来获得该类的 Class 对象。大部分时候，应该使用方式 2 来获取指定类的 Class 对象。因为方式 2 有如下优势：
- 代码更安全。编译阶段就可以检查需要访问的 Class 对象是否存在；
- 程序性能更好。

获取了 Class 对象之后可以进行的操作就多了，程序可以调用 Class 对象的方法来获得该对对象和对应类的真实信息了。

### 从 Class 中获取信息

Class 类提供了大量的**实例方法**来获取该 Class 对象所对应类的相关信息。下面的方法都可能提供了多个**重载**的版本。

#### 获取 Class 对象的对应类的构造器

- `Constructor<T> getConstructor(Class<?> ... parameterTypes)`：返回此 Class 对象对应类的、**带指定形参列表** 的 public 构造器；
- `Constructor<T>[] getConstructors()`：返回此 Class 对象对应类的**所有 public** 构造器；
- `Constructor<T> getDeclaredConstructor(Class<?> ... parameterTypes)`：返回此 Class 对象对应类的、**带指定形参列表** 的构造器，与构造器的访问权限无关！
- `Constructor<T>[] getDeclaredConstructors()`：返回此 Class 对象对应类的所有构造器，与构造器的访问权限无关！

#### 获取 Class 对象的对应类所包含的方法

- `Method getMethod(String name, Class<?> ... parameterTypes)`：返回此 Class 对象对应类的、带指定形参列表的** public 方法**
- `Method[] getMethods()`：返回此 Class 对象对应类的**所有 public 方法**；
- `Method getDeclaredMethod(String name, Class<?> ... parameterTypes)`：返回 Class 对象对应类的、带指定形参列表的方法，与方法的访问权限无关！
- `Method[] getDeclaredMethods()`：返回 Class 对象对应类的**全部方法**，与方法的访问权限无关！

#### 获取 Class 对象的对应类所包含的成员变量

- `Field getField(String name)`：返回此 Class 对象对应类的、指定名称的**public 成员变量**；
- `Fields[] getFileds()`：返回此 Class 对象对应类的**所有 public 成员变量**；
- `Field getDeclaredField(String name)`：返回此 Class 对象对应类的、指定名称的成员变量，与成员变量的访问权限无关！
- `Fields[] getDeclaredFields()`：返回此 Class 对象对应类的全部成员变量，与成员变量的访问权限无关！

还有很多其他的功能呢：
- 访问 Class 对象对应类上所包含的 Annotation；
- 访问 Class 对象对应类包含的内部类（`Class<?>[] getDeclaredClasses()`）；
- 访问 Class 对象对应类的所在的外部类（`Class<?> getDeclaringClass()`）；
- 获取 Class 对象对应类的修饰符、所在包、类名等基本信息

方法就不一一介绍了，详细可阅读 [Java Class API](https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html)。

{ % note success % }
观察上面方法的描述，其实大体可以总结出来，带 `Declared` 字眼的方法呢，返回的内容就不受访问权限的控制！
{% endnote %}

#### 方法理解

上面介绍了一群方法，怎么调用呢？传参是怎样的呢？看个栗子就明白了！

假设某个类包含如下三个 `info` 方法签名：
- `public void info()`
- `public void info(String str)`
- `public void info(String str, Integer num)`

这三个同名方法属于重载，参数列表不同。假如想要指定第 2 个 `info` 方法，那么形参列表为 `String.class`，因此程序中获取该方法应该使用如下代码：

```java
// clazz 是 Class 对象，第一个参数是方法名，后面的个数可变的 Class 参数形参类型列表
clazz.getMethod("info", String.class);
```

看了上面的例子，应该可以看懂 `Method getMethod(String name, Class<?> ... parameterTypes)` 这个方法如何使用了吧。这里仅是概览一下方法，下文会有更详细的示例。

## 使用反射生成并操作对象

Class 对象通过上面介绍的方法，可以获得该类里的方法（由 `Method` 对象表示）、构造器（由 `Constructor` 对象表示）、成员变量（由 `Field` 对象表示），这三个类都位于 `java.lang.reflect` 包下，并实现了 `java.lang.reflect.Member` 接口。

程序可以通过 `Method` 对象来执行对应的方法，通过 `Constructor` 对象来调用对应的构造器创建实例，能通过 `Field` 对象直接访问并修改对象的成员变量值。

### 创建对象

先使用 Class 对象获取指定的 Constructor 对象，再调用 Constructor 对象的 `newInstance()` 方法来创建该 Class 对象对应类的实例！

定义一个方法用来创建对应类名的 Java 对象：

```java
public class CreateObjectTest {

    static Object createObject(String clazzName) throws Exception {
        // 根据全限定的类名获取对应的 Class 对象
        Class<?> clazz = Class.forName(clazzName);
        // 使用 clazz 对应类的无参构造器创建实例
        return clazz.getConstructor().newInstance();
    }

    public static void main(String[] args) throws Exception {
        Object s = createObject("reflect.Student");
    }
}
```

其实，如果想调用有参的构造器创建对象，只要用上面介绍过的方法去获取有参的 `Contructor` 对象即可。然后调用 `newInstance` 方法时，传入对应的实参就行。

> Spring 框架就采用读取配置文件的内容，然后通过反射来创建对象。

{% note warning %}

通常没有必要使用反射来创建对象，因为反射创建对象时性能要稍低。实际上，只有当程序需要动态创建某个类的对象时才会考虑使用反射。通常在开发通用性比较广的框架、基础平台时可能会大量使用反射。

{% endnote %}

### 调用方法

----

> 生命不息，折腾不止！关注 「Coder 魔法院」，祝你 Niubilitiy ！🐂🍺