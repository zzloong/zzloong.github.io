---
title: Java 泛型知识笔记
date: 2020-07-13 12:49:10
tags:
  - Java
  - 基础
categories:
  - Java
keywords:
  - 泛型
  - Generic
---

![pastries-模板](https://gitee.com/michael_xiang/images/raw/master/uPic/pastries-756601_640.jpg)

## 泛型概念

泛型（ `Generic`）是一种编译器机制，您可通过该机制获取通用的代码并参数化（或模板化）剩余部分，从而以一种一般化方式创建（和使用）一些类型的实体（比如类或接口和方法）。这种编程方法被称为泛型编程。

所谓泛型，就是允许在定义类、接口、方法时使用类型形参，这个类型形参(或叫泛型)将在声明变量、创建对象、调用方法时动态地指定(即传入实际的类型参数，也可以称为「类型实参」)。

<!-- more -->

JDK 5.0（2004 年发布）向 Java 语言中引入了泛型类型（泛型）和关联的语法。增加泛型支持，很大程度上都是为了让集合能记住其元素的数据类型。在没有泛型之前，一旦把一个对象「丢进」 Java 集合中，集合就会忘记对象的类型，把所有的对象当成 Object 类型处理。当程序从集合中取出对象后，就需要进行强制类型的转换。这种强制类型转换不仅使代码臃肿，还很容器引起 `ClassCastException` 错误。

## 示例

先通过一个简单的示例了解一下泛型是什么样的。

```java
List strList = new ArrayList();
strList.add("Name");
strList.add("Aget");
// 不小心存入一个 Integer 对象
strList.add(new Integer(1));
//ArrayList可以存放任意类型，所以，下一行执行，并不会报错
System.out.println(strList);
//这一行强转就出错了
String str = (String)strList.get(2);
```

输出：

```java
[Name, Aget, 1]
Exception in thread "main" java.lang.ClassCastException: java.lang.Integer cannot be cast to java.lang.String
	at main.main(main.java:14)

Process finished with exit code 1
```

可以看到，我们 在给 List 中元素包含了两种类型：String 和 Interger 类型。 在 JDK 5.0 之前，Java 语言对此行为没有任何约束，这导致了许多编码错误。因为不知道 List 中包含的内容，则必须检查想要访问的元素，查看是否能处理这种类型的元素，否则可能会遇到 `ClassCastException`。

借助泛型，可以事先指定 List 中的元素类型，这样编译器在编译阶段就能够发现上面的问题：

```java
List<String> strList = new ArrayList();
strList.add("Name");
strList.add("Aget");
// 下面这一行编辑器就会提示错误，编译就不会通过
strList.add(new Integer(1));
System.out.println(strList);
String str = (String)strList.get(2);
```

![Jietu20190713-102223-listerror.jpg](https://i.loli.net/2019/07/13/5d29407987abd35714.jpg)

从 Java 5 之后，Java 引入了「参数化类型」(`parameterized type`)的概念。允许程序在创建集合时指定集合元素的类型。Java 的参数化类型被称为「泛型」(`Generic`)。

上面的 `List<String>`，可以称 List 是带一个类型参数的泛型接口，类型参数（类型实参）是 `Sting`。集合记住了所有元素的数据类型，从而无须对集合元素进行数据类型转换。

在 Java 7 之前，如果使用带有泛型的接口、类定义变量，那么调用构造器创建对象时，构造器的构面也要带上泛型：

```java
List<String> strList = new ArrayList<String>();
```

从  Java7 开始， Java 允许在构造器后不需要带完整的泛型信息，只需要给出尖括号即可，Java 可以自动推断出尖括号里的是什么泛型信息：

```java
List<String> strList = new ArrayList<>();
```

## 常见的 T，E，K，V，？

本质上这些个都是通配符，没啥区别，只不过是编码时的一种约定俗成的东西。比如上述代码中的 T ，我们可以换成 A-Z 之间的任何一个 字母都可以，并不会影响程序的正常运行，但是如果换成其他的字母代替 T ，在可读性上可能会弱一些。通常情况下，T，E，K，V，？ 是这样约定的：

- ？ 表示不确定的 java 类型
- T (type) 表示具体的一个java类型
- K V (key value) 分别代表java键值中的Key Value
- E (element) 代表Element

## 泛型的应用

泛型有三种使用方式：

1. 泛型类
2. 泛型接口
3. 泛型方法

### 泛型类

泛型类型用于类的定义中，这个类称为「泛型类」。最典型的泛型类就是各种容器类，例如：List、Set、Map。

泛型类的定义语法：

```java
class 类名称<泛型标识>{
    private 泛型标识 成员变量；
    ……
}
```

- 泛型标识：可以随便写任意标识号，标识指定的泛型的类型，比如常用的 `T`

举个栗子：

```java
// 泛型类
class Card<T> {
    private T id;

    public T getId() {
        return id;
    }

    public void setId(T id) {
        this.id = id;
    }

    public Card(T id){
        setId(id);
    }
}
// main 函数
Card<String> card1 = new Card<>("One");
System.out.println("card1 id is: "+card1.getId());

Card<Integer> card2 = new Card<>(2);
System.out.println("card2 id is: "+card2.getId());

// 输出
card1 id is: One
card2 id is: 2
```

怎么样，看到区别了吗？同样一个类创建的两个实例，成员变量名都是 `id`，但是二者的类型却可以不同。`T` 感觉有种 `template` 的意思，模板嘛，占坑渲染即可。

定义的泛型类实例化对象时并不一定要春如泛型类型实参。比如一开始的例子 `ArrayList`，当不传入泛型类型实参时，默认的就是 `Object` 类型。这时候，就不会起到限制类型的作用了。

注意点：

1. 泛型的类型参数只能是「类类型」，不能是原始类型（`Primitive Type`）——byte/short/ int/long/float/ double/ boolean/char
2. 定义泛型类中的构造器时，构造器名还是原来的类型，不需要增加泛型声明。
3. 不能对确切的泛型类型使用 `instanceof`操作，比如 `card1 instanceof Card<String>`，会报 `instanceof 的泛型类型不合法` 错误，但是 `card1 instance Card` 是不会报错的，并返回 `true`。因为不管泛型的实际类型参数是什么，他们在运行时总有同样的类，对于 Java 来说，它们依然被当成同一个类处理，在内存中也只占用一块内存空间。

`List<Integer>` 并不是 `List<Number>` 的子类！
`List<Integer>` 并不是 `List<Number>` 的子类！
`List<Integer>` 并不是 `List<Number>` 的子类！

### 泛型接口

泛型接口和泛型类的定义语法差不多。泛型接口经常被用在各种类的生成器中。

```java
//定义泛型接口
public interface Generaotr<T> {
    public T next();
}
```

泛型接口实现类，未传入泛型实参时：

```java
public class FruitGenerator<T> implements Generaotr<T> {
    // 这个 Override 可不能少
    @Override
    public T next(){
        return null;
    }
}
```

接口实现类定义时，也需要将泛型的声明加上。如果不加，例如`public class FruitGenerator implements Generaotr<T>`，这样就会报错，`cannot resolve symbol T`。

泛型接口实现类，传入泛型实参，比如 `String`：

```java
public class FruitGenerator implements Generaotr<String> {
    private String[] fruits = new String[]{"Apple", "Banana", "Pear"};

    @Override
    public String next(){
        Random rand = new Random();
        return fruits[rand.nextInt(3)];
    }
}
```

这里我们传入了泛型实参，接口实现类中的泛型都改为了这个传入的实参。并且，此时，接口实行类就不用加上泛型声明了，加入的话，反而会提示 `attempting to use incompatible return type`错误。

### 泛型通配符

`Integer` 是 `Number` 的一个子类。那么，在使用 `Card<Numer>` 作为方法形参的方法中，是否可以传入 `Card<Interger>` 的实参呢？

```java

private static void showKeyValue1(Card<Number> card){
    System.out.println(card.getId());
}
// 测试
Card<Integer> card2 = new Card<>(2);
System.out.println("card2 id is: " + card2.getId());

showKeyValue1(card2);
```

编译器会报错 ：

```java
Generic<java.lang.Integer> cannot be applied to Card<java.lang.Number>
```

通过提示信息我们可以看到 `Card<Integer>` 不能被看作为 `Card<Number>` 的子类。

{% note warning no-icon %}
如果 Apple 是 Fruit 的一个子类型（子类或者子接口），G 是一个具有泛型声明的类或接口，`G<Apple>` 并不是 `G<Fruit>` 的子类型！这一点值得注意，因为与大部分人第一感觉是不同的。
{% endnote %}

如何解决上面的问题呢？

我们需要在逻辑上引入一个同时是 `Card<Number>` 和 `Card<Interger` 父类的引用类型。由此，「类型通配符」产生了：

```java
// 其实，这里不用通配符，直接是 Card 也会 OK
private static void showKeyValue1(Card<?> card){
    System.out.println(card.getId());
}
```

类型通配符一般是使用 `?` 代替具体的类型实参。注意，`?` 是类型实参，不是类型形参。简单理解，这里的 `?` 就和 `Number`、`String` 一样，都是一种实际的类型。可以把 `?` 看成所有类型的父亲，是一种真实的类型，当成 Object 来处理。

#### 通配符上界

为了表示限制类型，泛型提供了被限制的通配符。通配符上界使用 `<? extends T>` 的格式，意思是需要一个 T 类型或者 T 类型的子类，一般T 类型都是一个具体的类型。示例如下：

```java
public void printIntValue(List<? extends Number> list) {
    for (Number number : list) {
        System.out.print(number.intValue()+" ");
    }
}
```

此处，未知类型一定是 `Number` 的子类型，因此，可以把 `Number` 称为这个通配符的上界(`upper bnound`)。

写入操作过以上给定的赋值语句，你能把一个什么类型的元素合法地插入到 list 中呢？
- 你不能插入一个 Integer 元素，因为 list 可能指向 `List<Double>`。
- 你不能插入一个 Double 元素，因为 list 可能指向 `List<Integer>`。
- 你不能插入一个 Number 元素，因为 list 可能指向 `List<Integer>`。

你不能往 `List<? extends T>` 中插入任何类型的对象，因为你不能保证列表实际指向的类型是什么，你并不能保证列表中实际存储什么类型的对象。唯一可以保证的是，你可以从中读取到 T 或者 T 的子类。

#### 通配符下界

除了可以指定通配符的上限之外，Java 还允许指定通配符的下限。通配符下界使用 `<? super T>` 的格式，意思是需要一个T类型或者 T 类型的父类，一般 T 类型都是一个具体的类型。示例如下：

```java
public void fillNumberList(List<? super Number> list) {
    // Number 及它子类的实例就可以加入 list 中
    list.add(new Integer(0));
    list.add(new Float(1.0));
}
```

其实，Java 泛型不仅允许在使用通配符形参时设定上限，还可以在定义泛型形参时设定上限，用以表示传给泛型形参的实际参数类型要么是该上限类型，要么是该上限类型的子类。

```java
public class Apple<T extends Number> {
    T col;

    public static void main(String[] args) {
        Apple<Integer> ai = new Apple<>();
        Apple<Double> ad = new Apple<>();
        // 下面编译就会出错，因为设置了上限是 Number
        Apple<String> as = new Apple<>();
    }
}
```

![Jietu20190713-111233-bound.jpg](https://i.loli.net/2019/07/13/5d294c385b14039332.jpg)

#### 总结 PECS

{% note success no-icon %}

上面的知识点可以概括为 PECS 原则，方便记忆。
- Producer 生产者，则用 extends。因为你想从列表中获取 T 类型的的元素。看着就像是「生产」元素的效果。不能明确存入元素的类型，因此它的主要总用不在「存」。
- Conumer 消费者，则用 super。想把 T 类型的子元素放入到列表中。不能保证明确读取到的元素的类型，因此它的主要作用不在于「读」。

{% endnote %}

详细理解这里的 PECS 原则，还可以阅读：
- [知乎/Java 泛型 <? super T> 中 super 怎么 理解？与 extends 有何不同？](https://www.zhihu.com/question/20400700/answer/117464182)。
- [并发编程网/泛型中? super T和? extends T的区别](http://ifeve.com/difference-between-super-t-and-extends-t-in-java/) [原文 URL](https://stackoverflow.com/questions/4343202/difference-between-super-t-and-extends-t-in-java)

### 泛型方法

在一些情况下，定义类、接口时没有使用泛型形参，但定义方法时想自己定义泛型形参，这也是可以的。Java 5 提供了对泛型方法的支持。

判断一个方法是否是泛型方法关键看方法返回值前面有没有使用 `<>` 标记的类型，有就是，没有就不是。

假设需要实现这样一个方法：将一个 Object 数组所有的元素添加到一个 Collection 集合中。

```java
static void fromArrayToCollection(Object[] a, Collection<Object> c) {
    for (Object o : a) {
        c.add(o);
    }
}
```

下面这么写将会引起编译错误：

```java
String[] strArr = {"a","b"};
List<String> strList = new ArrayList<>();
// Collection<String>对象不能当成 Collection<Oject>使用
fromArrayToCollection(strArr, strList);
System.out.println(strList);
```

`Collection<String>` 并不是 `Collection<Object>` 的子类型，所以上面这个方法只能将 `Object[]` 数组中的元素复制到元素为 `Object` 的 Collection 集合中。

`Collection<Object> c` 改为通配符 `Collection<?> c` 是否可行呢？也不行。

为了解决上面这个问题，可以使用泛型方法(`Generic Method`)。所谓泛型方法，就是在声明方法时，定义一个或多个泛型形参。多个泛型形参声明放在方法修饰符和方法返回值类型之间。泛型方法语法格式如下：

```java
修饰符 <T,S,...> 返回值类型 方法名(形参列表){
    // 方法体
}
```

上面的方法改进如下：

```java
static <T> void fromArrayToCollection(T[] a, Collection<T> c) {
    for (T o : a) {
        c.add(o);
    }
}
```

与泛型类和泛型接口的使用不同，泛型方法中的泛型形参不需要显示传入实际参数类型。编译器会根据实参推断泛型所代表的类型。但是小心，避免制造迷惑。比如下面的栗子：

```java
public class Main {
    public static void main(String[] args) {

        List<String> as = new ArrayList<>();
        List<Object> ao = new ArrayList<>();

        // 下面会编译错误
        test(as, ao);
    }

    static <T> void test(Collection<T> from, Collection<T> to) {
        for (T ele : from) {
            to.add(ele);
        }
    }
}
```

![Jietu20190713-121930-method-error.jpg](https://i.loli.net/2019/07/13/5d295bf0741a761687.jpg)

`test` 方法传入两个实参，`ao` 的数据类型是 `List<Object>`，而 `as` 的数据类型是 `List<String>`。与泛型方法签名进行对比，`test(Collection<T> from, Collection<T> to)`，编译器无法正确识别 T 所代表的实际类型。为了避免这种错误，可以改为如下形式：

```java
static <T> void test(Collection<? extends T> from, Collection<T> to) {
    for (T ele : from) {
        to.add(ele);
    }
}
```

`Collection<? extends T>` 这种采用类型通配符并设置通配符上限的表示方式，只要 test 方法的第一个 Collection 集合里的元素类型是后一个 Collection 集合里元素类型的子类即可。

## 补充

对于类中变量的类型，绘制了一个简单的图：

![变量](https://i.loli.net/2019/07/13/5d2961d5155f246541.png)

作图工具：[ProcessOn](https://www.processon.com/i/55ddb6bae4b04fe84c504c5f)，强烈安利

## 总结

经过上面的学习，我对泛型(`Generic`)有了初步的认识。类似于一个「模板」的概念，「占坑」等着渲染，提供了对类、接口、方法定义的灵活性，使用起来具有了「动态性」。当然，这只是我的个人理解，方便记忆的，可能表述并不是很准确。在实际的生产代码中，也有应用泛型类、泛型方法的，能够想到去使用它，无疑需要对泛型有比较好的理解。

## 参考

- [Oracle-Generics Documentation](https://docs.oracle.com/javase/tutorial/java/generics/index.html)
- [CSDN-java 泛型详解-绝对是对泛型方法讲解最详细的，没有之一](https://blog.csdn.net/s10461/article/details/53941091)
- [CSDN-ShuSheng007-秒懂Java泛型](https://blog.csdn.net/ShuSheng0007/article/details/80720406#%E5%A6%82%E4%BD%95%E8%B0%83%E7%94%A8%E4%B8%80%E4%B8%AA%E6%B3%9B%E5%9E%8B%E6%96%B9%E6%B3%95)
- [掘金-深入理解Java泛型](https://juejin.im/post/5b614848e51d45355d51f792)
- [重新认识Java——泛型（基础、使用和实现原理）](http://hinylover.space/2016/06/25/relearn-java-generic-1/)
- [glmapper—聊一聊-JAVA 泛型中的通配符 T，E，K，V，？](http://www.glmapper.com/2019/08/19/base-java-generics/#%E5%B8%B8%E7%94%A8%E7%9A%84-T%EF%BC%8CE%EF%BC%8CK%EF%BC%8CV%EF%BC%8C%EF%BC%9F)
