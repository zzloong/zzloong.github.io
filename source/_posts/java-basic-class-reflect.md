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

{% note info no-icon %}

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

看个栗子：

Student.java:
```java
public class Student{
    String name;

    public Student() {
    }

    private Student(String name) {
        System.out.println("My name is: " + name);

    }

    public void hello(String content) {
        System.out.println("name: " + name + " say :" + content);
    }
}
```

CreateObjectTest.java:

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


{% note warning no-icon %}

通常没有必要使用反射来创建对象，因为反射创建对象时性能要稍低。实际上，只有当程序需要动态创建某个类的对象时才会考虑使用反射。通常在开发通用性比较广的框架、基础平台时可能会大量使用反射。

{% endnote %}

### 调用方法

通过反射调用方法其实和上面的步骤差不多：获得某个类的 Class 对象，通过该对象的 `getMethods()` 方法或者 `getMethod()` 方法获取全部方法或指定方法。具体语法在上面介绍过。方法返回值是 `Method` 数组或者 `Method` 对象。

每个 `Method` 对象对应一个方法，程序通过该 `Method` 调用它对应的方法。`Method` 包含一个 `invoke()` 方法，方法签名如下：
- `Object invoke(Object obj,Object ... args)`：`obj` 是执行该方法的主调，`args` 是执行该方法时传入的实参。

接上面的示例继续完善，CreateObjectTest.java：

```java
public class CreateObjectTest {

    static Object createObject(String clazzName) throws Exception {
        Class<?> clazz = Class.forName(clazzName);
        return clazz.getConstructor().newInstance();
    }

    static void methodTest(Object target) throws Exception {
        // 通过实例的 `getClass` 方法获取 Class 对象
        Class<?> clazz = target.getClass();
        // 通过 Class 对象获取对应类的 Method 对象
        Method mtd = clazz.getMethod("hello", String.class);
        // 调用 Method 对象的 invoke 方法，传入方法实参
        mtd.invoke(target, "测试");
    }

    public static void main(String[] args) throws Exception {
        Object s = createObject("reflect.Student");
        methodTest(s);
    }
}
```

输出：
```
name: null say :测试
```

这里因为调用的是 Student 无参构造器，因此 name 为空。

Method 的 `invoke()` 方法来调用对应方法时，Java 会要求程序具有调用该方法的权限。默认情况，`private` 方法是无权调用的。可以通过先调用 Method 对象的 `setAccessible(boolean flag)` 方法取消 Java 语言的访问权限检查（设为 `false` 时，不检查）。

> Spring 框架将成员变量的值以及依赖对象等都放在配置文件中，然后采用上面方式进行创建对象、赋值成员变量的。这也是 Spring 框架 IoC 的秘密。

{% note info no-icon %}

上面这个是《疯狂 Java 讲义》中的提示，从这提示里可以看到，反射的重点意义其实不在于它能够创建对象、赋值变量，因为这通过构造器等也能做，我觉得它存在的主要意义在于能够在**运行时**动态地执行创建对象、赋值变量等操作。

{% endnote %}

### 访问成员变量值

通过 Class 对象的 `getFields()` 或 `getField()` 方法可以获取该类所包括的全部成员变量或指定成员变量。Field 提供了下面两组方法来读取或设置成员变量值。
- `getXxx(Object obj)` 获取 `obj` 对象的该成员的变量值。此处 Xxx 对应 8 中基本类型，如果该成员变量类型是引用类型，则取消 get 后面的 Xxx
- `setXxx(Object obj, Xxx val)` 将 `obj` 对象的该成员变量设置成 val 值。如果该成员变量类型是引用类型，则取消 set 后面的 Xxx

栗子：
```java
class Person {
    private String name;
    private Integer age;

    public Person() {
    }

    public Person(String name, Integer age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

public class FieldTest {
    public static void main(String[] args) throws Exception {
        // 创建一个 Person 对象
        Person p = new Person();
        // 获取 Class 对象
        Class<Person> personClazz = Person.class;
        // 使用 getDeclaredField() 方法获取 private 类型的成员变量
        Field nameFiled = personClazz.getDeclaredField("name");
        // 能够获取到并不代表能够访问成员变量，需要设置可访问才行，否则会报错：
        // Class reflect.FieldTest can not access a member of class reflect.Person with modifiers "private"
        nameFiled.setAccessible(true);
        nameFiled.set(p, "Michael");
        Field ageFiled = personClazz.getDeclaredField("age");
        ageFiled.setAccessible(true);
        // 调用 setInt() 方法为 p 对象的 age 成员变量设置值
        // ageFiled.setInt(p, 30);
        ageFiled.set(p, 30);
        System.out.println(p);

    }
}
```

上面代码中，如果使用 `ageFiled.setInt(p,30)` 则会报如下错误：
```
Exception in thread"main"java.lang.IllegalArgumentException:Can not set java.lang.Integer field reflect.Person.age to(int)30
at sun.reflect.UnsafeFieldAccessorImpl.throwSetIllegalArgumentException(UnsafeFieldAccessorImpl.java:167)
at sun.reflect.UnsafeFieldAccessorImpl.throwSetIllegalArgumentException(UnsafeFieldAccessorImpl.java:191)
at sun.reflect.UnsafeObjectFieldAccessorImpl.setInt(UnsafeObjectFieldAccessorImpl.java:114)
at java.lang.reflect.Field.setInt(Field.java:949)
at reflect.FieldTest.main(FieldTest.java:50)
```

因为 `age` 不是基本类型，要用上面那种写法才会 OK。

### 操作数组

在 `java.lang.reflect` 包下提供了一个 Array 类，Array 对象可以代表所有的数组。可以通过 Array 动态地创建数组、操作数组元素。

- `static Object newInstance(Class<?> componentType,int ... length)`：创建一个具有指定的元素类型、指定维度的新数组
- `static xxx getXxx(Object  array, int index)`：返回 array 数组中第 index 个元素。其中，xxx 是各种基本数据类型，如果数据元素时引用类型，则方法为 `get(Object array, int index)`
- `static setXxx(Object array, int index, xxx value)`：设置 array 数组中第 index 个元素值为 value。xxx 表示基本类型，如果是引用引用类型，则方法为 `set(Object array, int index, Object value)`。

具体的用法，可以阅读 [java.lang.reflect.Array](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Array.html)

栗子：

```java
public class ArrayTest {
    public static void main(String[] args) {
        try {
            Object arr = Array.newInstance(String.class, 10);
            Array.set(arr, 5, "反射测试");
            Array.set(arr, 6, "数组学习");
            Object c1 = Array.get(arr, 5);
            Object c2 = Array.get(arr, 6);
            System.out.println(c1);
            System.out.println(c2);
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

## 其他

- `java.lang.reflect` 包下还有 Proxy 类和一个 `InvocationHandler` 接口，通过他们可以生成 JDK 动态代理或动态代理对象。代理对象具有原本对象的执行方法之外，还增加了可以增加以下额外的行为（AOP 里方法在执行目标前、之后插入一些通用处理的效果）。

## 参考

- [腾讯云社区/深入理解 Java 反射：Field （成员变量）](https://cloud.tencent.com/developer/article/1015078)
- 《疯狂 Java 讲义》第四版，18 章

----

> 生命不息，折腾不止！关注 「Coder 魔法院」，祝你 Niubilitiy ！🐂🍺