---
title: Java 基础 —— 注解 Annotation
date: 2020-07-04 12:49:10
tags:
  - 注解
categories:
  - Java
keywords:
  - annotation
---

## 简介
- Annotation 是从 JDK 5.0 引入的。
- 注解使得我们可以以编译器验证的格式存储程序的额外信息。注解可以生成描述符文件，甚至是新的类定义，并且有助于减轻编写“样板”代码的负担。

比如，我们项目中常常使用的 `lombok` 包中的注解，`@Data`、`@NoArgsConstructor`、`@AllArgsConstructor` 等注解，大大简化了代码，省了很多操作。

## 基本注解

Java 提供了几个基本注解，这些内置注解它们都位于 `java.lang` 包下。

- `@Override` 作用于子类中的方法，表示该方法一定要重写父类中的方法，可以有效避免子类方法名误写错误；
- `@Deprecated`表示某类、方法等已过时，当它们被使用时，编译器将会给出警告；
- `@SuppressWarnings` 镇压警告，被修饰的程序元素及其子元素取消显示指定的编译器警告，`supress` 有抑制、废止的含义；
- `@SafeVarags` Java 7 新增的，抑制堆污染警告；
- `@FunctionalInterface`：Java 8 中加入用于表示类型声明为函数式接口

## 注解示例

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface TestAnnotation {
	
	public int id() default -1;
	
	public String msg() default "Hi";

}
```

说明：
- 通过关键字 `@interface` 定义，并不是接口；
- 注解的属性也叫做成员变量。注解只有成员变量，没有方法。注解的成员变量在注解的定义中以“无形参的方法”形式来声明，其方法名定义了该成员变量的名字；
- 这里的示例上使用了一些注解，称为「元注解」，后面会详细介绍，稍安勿躁，这里仅仅是 show 一下自定义的注解长什么样；

注意点：
- 如果一个注解内仅仅只有一个名字为 `value` 的属性时，应用这个注解时可以直接接属性值填写到括号内；
- 有一种情况是一个注解没有任何属性，在应用这个注解的时候，括号都可以省略，不包含任何元素的注解称为标记注解（`marker annotation`）；
- 在注解中定义属性时它的类型必须是 8 种基本数据类型外加 类、接口、注解及它们的数组；

## 元注解

JDK 除了在 `java.lang` 下提供了 4 个基本的 Annotation 之外，还在 `java.lang.annotation` 包下提供了4个 `Meta Annotation`——元注解。这 4 个注解用于修饰其他的注解。元注解是可以注解到注解上的注解，或者说元注解是一种基本注解，但是它能够应用到其它的注解上面。

### @Retention

Retention 的英文意为保留期的意思。当 `@Retention` 应用到一个注解上的时候，它解释说明了这个注解的的存活时间。

- `RetentionPolicy.SOURCE` 注解只在源码阶段保留，在编译器进行编译时它将被丢弃忽视；
- `RetentionPolicy.CLASS` 注解只被保留到编译进行的时候，它并不会被加载到 JVM 中；
- `RetentionPolicy.RUNTIME` 注解可以保留到程序运行的时候，它会被加载进入到 JVM 中，所以在程序运行时可以获取到它们；

### @Documented

它的作用是能够将注解中的元素包含到 Javadoc 中去。

### @Target

`@Target` 指定了注解运用的地方，规定了只能张贴到方法上、类上、方法参数上等等。@Target 有下面的取值：
- `ElementType.TYPE` 可以给一个类型进行注解，比如类、接口、枚举
- `ElementType.METHOD` 可以给方法进行注解
- `ElementType.FIELD` 可以给属性进行注解
- `ElementType.CONSTRUCTOR` 可以给构造方法进行注解
- `ElementType.LOCAL_VARIABLE` 可以给局部变量进行注解
- `ElementType.PACKAGE` 可以给一个包进行注解
- `ElementType.PARAMETER` 可以给一个方法内的参数进行注解
- `ElementType.ANNOTATION_TYPE` 可以给一个注解进行注解


### @Inherited

Inherited 是继承的意思，但是它并不是说注解本身可以继承，而是说如果一个超类被 @Inherited 注解过的注解进行注解的话，那么如果它的子类没有被任何注解应用的话，那么这个子类就继承了超类的注解。

## 自定义注解

使用 `@interface` 自定义注解时，自动继承了 `java.lang.annotation.Annotation` 接口。

一般我们使用注解，常常和验证器配合着使用。另外一种就是利用 AOP 结合使用。下面我们结合具体的示例来加深一下注解的使用。

### @Length 注解

下面，我们自定义一个注解，用来实现限制字段的长度。

```java
@Target({ElementType.FIELD, ElementType.CONSTRUCTOR, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Constraint(validatedBy = {LengthValidator.class})  // 与约束注解关联的验证器
public @interface Length {
    // 约束注解验证时的输出消息
    String message() default "限制字段值的范围";

    // 约束注解在验证时所属的组别
    Class<?>[] groups() default {};

    // 约束注解的有效负载
    Class<? extends Payload>[] payload() default {};

    int min() default 0;

    int max() default 100;
}
```

接着，我们定义对应的验证器：

```java
public class LengthValidator implements ConstraintValidator<Length, Integer> {
    private Integer min;
    private Integer max;

    @Override
    public void initialize(Length constraintAnnotation) {
        this.min = constraintAnnotation.min();
        this.max = constraintAnnotation.max();
    }

    @Override
    public boolean isValid(Integer value, ConstraintValidatorContext context) {
        if (value == null) {
            return true;
        }
        if (value >= min && value <= max) {
            return true;
        }
        return false;
    }
}
```

注意点：

- 只有当注解中有 `value` 成员变量时，才可以不传 `key`，因为它默认是将值赋给 `value` 成员变量的；
- 成员变量没有使用 `default` 定义默认值，在使用注解时，就必须要对它进行赋值，有默认值则可以不必赋值；
- 如果只有一个参数成员，一般参数名为 `value`；
- `but does not contain a groups parameter` 注解需要定义成员变量 `groups`
- `xxx contains Constraint annotation, but does not contain a payload parameter.` 注解需要定义成员变量 `Payload`

## 提取注解信息

AnnotatedElement 接口是所有程序元素（如Class、Method、Constructor等）的父接口，所以程序通过反射获取了某个类的 AnnotatedElement 对象（如Class、Method、Constructor等）之后，程序就可以调用该对象的如下3个方法来访问Annotation信息：

- `getAnnotation(Class<T> annotationClass)` 返回该程序元素上指定类型的注释，如果不存在，则返回 null，比如 `Length length = field.getAnnotation(Length.class);` 通过反射获取到该字段上使用的 `@Length` 注解的详细信息；
- `Annotation[] getAnnotations()` 返回该程序元素上的所有注释，例如 `Annotation annotations = Class.forName("Test").getMethod("info").getAnnotations();` 表示获取 `Test` 类中 `info` 方法上的注释；
- `boolean isAnnotatinPresent(Class<?extends Annotation> annotationClass)` 判断该元素上是否存在指定类型的注释，如果存在则返回 true，否则返回 false，例如 `methodA.isAnnotationPresent(Length.class)`，表示判断方法 methodA 上是否用了 `@Length`注释；

> 为了获得程序中的程序元素（如 Class 、Method等），必须使用反射知识。

```java
// 获取某个对象的类
Class cl = obj.getClass();
// 获取某个类中定义的所有属性
Fields[] fields = cl.getDeclaredFields();
```

## 编译时处理 Annotation

如果希望让程序中的 Annotation 在运行时起一定的作用，只有通过某种配套的工具对Annotation中的信息进行访问和处理，访问和处理 Annotation 的工具统称 APT（Annotation ProcessingTool）。

Annotation 处理器在处理 Annotation 时可以根据源文件中的Annotation生成额外的源文件和其他的文件（文件的具体内容由 Annotation 处理器的编写者决定），APT 还会编译生成的源代码文件和原来的源文件，将它们一起生成 class 文件。

> 我们可以在Java源文件中放置一些Annotation，然后使用APT工具就可以根据该 Annotation 生成另一份 XML 文件，这就是 Annotation 的作用。

## 参考

- [On Java8 —— 第二十三章 注解](https://lingcoder.github.io/OnJava8/#/book/23-Annotations?id=%e7%ac%ac%e4%ba%8c%e5%8d%81%e4%b8%89%e7%ab%a0-%e6%b3%a8%e8%a7%a3)
- [csdn-frank909-秒懂，Java 注解 （Annotation）你可以这样学](https://blog.csdn.net/briblue/article/details/73824058)
- [啥？听说你还在手写复杂的参数校验？](https://mp.weixin.qq.com/s?__biz=MzU4ODI1MjA3NQ==&mid=2247484955&idx=1&sn=29b0f228415abd8542d07c898e6398ba&chksm=fdded0dfcaa959c9d9fc9d04130409c392460561bdce53996261fe8973b81f4811a8ebe5c357&scene=21#wechat_redirect)
- [使用 spring validation 完成数据后端校验](https://www.cnkirito.moe/spring-validation/)
- [csdn-Bean Validation——自定义注解](https://blog.csdn.net/ZZY1078689276/article/details/79064835)
