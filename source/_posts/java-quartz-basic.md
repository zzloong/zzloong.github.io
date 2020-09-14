---
title: Java 定时任务框架 Quartz 入门篇
date: 2020-09-06 12:49:10
tags:
  - Java
  - 定时任务
  - Quartz
categories:
  - Java
keywords:
  - Quartz
---

## Quartz 概念

Quartz 是任务调度的开源项目。Quartz 就是基于 Java 实现的任务调度框架。

- Quartz 官网地址：http://www.quartz-scheduler.org
- Quartz 官网 - Examples：http://www.quartz-scheduler.org/documentation/quartz-2.3.0/tutorials/

## Quartz 运行环境

- Quartz 可以运行嵌入在两一个独立式应用程序
- Quartz 可以在应用程序服务器（或 Sevelet 容器） 内被实例化，并参与事务
- Quartz 可以作为独立的程序运行（其自己的 Java 虚拟机内），可以通过 RMI 使用
- Quartz 可以被实例化，作为独立的项目集群（负载平衡和故障转移功能），用于作业的执行

## Quartz 设计模式

- Buider 模式
- Factory 模式
- 组件模式
- 链式编程

> 这里说的设计模式，其实主要是针对 Quartz 使用过程中涉及的一些有用法。比如，通过 Factory 模式，生成调度器。比如，使用 Quartz 涉及了调度器、Job、触发器等组件。比如可通过 Build 方法创建出组件。

## Quartz 核心概念

### 任务 Job 和 JobDetail

Job 指的是你想要执行的任务类，每一个 Job 必须实现 `org.quartz.job` 接口，且只需要实现接口定义的 `execute()` 方法。在 `execute` 方法内编写任务的业务逻辑。

Job 实例在 Quartz 中的生命周期：每次调度器执行 Job 时，它在调用 `execute` 方法前会创建一个新的 Job 实例，当调用完成之后，关联的 Job 对象实例会被释放，释放的实例会被垃圾回收机制回收。

JobDetail: JobDetail 为 Job 实例提供许多设置属性，以及 jobDataMap 成员变量属性，它用来存储特定 Job 实例的状态信息，调度器需要借助 JobDetail 对象来添加 Job 实例。

### 触发器 Trigger

Trigger 为你执行任务的触发器，比如你想每天定时 3 点发送一份统计邮件，Trigger 将会设置 3 点进行执行该任务。

Trigger 主要包含两种：
- `SimpleTrigger`
- `CronTrigger`

### 调度器 Scheduler

Scheduler 为任务的调度器，它将任务 Job 及 触发器 Trigger 整合起来，负责基于 Trigger 设定的时间来执行 Job。

## Quartz 的体系结构

![cpbdMt](https://gitee.com/michael_xiang/images/raw/master/uPic/cpbdMt.png)

## Quartz 几个常用 API

以下是 Quartz 编程 API 几个重要接口，也是 Quartz 的重要组件。

- Scheduler 用于与调度程序交互的主程序接口

Scheduler 调度程序-任务执行计划表，只有安排进执行计划的任务 Job（通过 schduler.scheduleJob 方法安排进执行计划），当它预先定义的执行时间到了（任务触发器 trigger），该任务才会执行。

- Job 我们预先定义的希望在未来时间能够被调度程序执行的任务类，我们可以自定义。
- JobDetail 使用 jobDetail 来定义定时任务的实例，jobDetail 实例是通过 JobBuilder 类创建的。
- JobDataMap 可以包含不限量的（序列化）的数据对象，在 job 实例执行的时候，可以使用其中的数据。JobDataMap 是 Java Map 接口的一个实现，额外增加了一些便于存取基本类型的数据方法。
- Trigger 触发器，Trigger 对象是用来触发执行 Job 的。当调度一个 Job 时，我们实例触发器然后调整它的属性来满足 Job 执行的条件。表明任务在什么时候会执行。定义了一个已经被安排的任务将会在什么时候执行的时间条件，比如每 2 秒执行一次。
- JobBuilder 用于声明一个任务实例，也可以定义关于该任务的详情，比如任务名、组名等。这个声明的实例将会作为一个实际执行的任务。
- TriggerBuilder 触发器创造器，用于创建触发器 trigger 实例。
- JobListener、TriggerListener、SchedulerListener 监听器，用于对组件的监听。

> 注意 Job 和 JobDetail 的区分，Job 是指我们具体的「任务类」。JobDetail 指通过 JobBuilder 类创建出的一个实例，它和 Job 类进行了绑定。

贴一个官网示例片段：

```java
  // define the job and tie it to our HelloJob class
  JobDetail job = newJob(HelloJob.class)
      .withIdentity("job1", "group1")
      .build();

  // Trigger the job to run now, and then repeat every 40 seconds
  Trigger trigger = newTrigger()
      .withIdentity("trigger1", "group1")
      .startNow()
            .withSchedule(simpleSchedule()
              .withIntervalInSeconds(40)
              .repeatForever())
      .build();

  // Tell quartz to schedule the job using our trigger
  scheduler.scheduleJob(job, trigger);
```

## 入门案例

### 引入 Maven 依赖

在 https://mvnrepository.com 官网搜索 quartz，选择了最新版本 [2.3.2](https://mvnrepository.com/artifact/org.quartz-scheduler/quartz/2.3.2)

```xml
<dependencies>
    <!-- 核心包 -->
    <!-- https://mvnrepository.com/artifact/org.quartz-scheduler/quartz -->
    <dependency>
        <groupId>org.quartz-scheduler</groupId>
        <artifactId>quartz</artifactId>
        <version>2.3.2</version>
    </dependency>
    <!-- 工具包 -->
    <!-- https://mvnrepository.com/artifact/org.quartz-scheduler/quartz-jobs -->
    <dependency>
        <groupId>org.quartz-scheduler</groupId>
        <artifactId>quartz-jobs</artifactId>
        <version>2.3.2</version>
    </dependency>
</dependencies>
```

说明：核心依赖是 `quartz`，而 `quartz-jobs` 是一个可选依赖，它主要放置了一些 quartz 的工具代码、一些预定义的 job。

### 任务类

```java
public class QuartzHelloJob implements Job {
    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        // 当前时间
        Date date = new Date();
        DateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd:HH:mm:ss");
        String dateStr = dateFormat.format(date);
        // 工作内容
        System.out.println("正在进行数据备份任务的定时执行，执行时间：" + dateStr);
    }
}
```

### 主类

```java
public class QuartzHelloScheduler {
    public static void main(String[] args) throws Exception {
        // 1. 调度器 Schduler，从工厂中获取调度器实例
        Scheduler stdScheduler = StdSchedulerFactory.getDefaultScheduler();

        // 2. 任务实例 JobDetail
        // 参数 1：任务的名称；参数 2：任务组的名称
        JobDetail jobDetail = JobBuilder.newJob(QuartzHelloJob.class)
                .withIdentity("job1", "jobGroup1")
                .build();

        // 3. 触发器 Trigger
        // 参数 1：触发器的名称；参数 2：触发器组的名称
        Trigger trigger = TriggerBuilder.newTrigger()
                .withIdentity("trigger1", "triggerGroup1")
                .withSchedule(SimpleScheduleBuilder.simpleSchedule().repeatSecondlyForever(5))
                .startNow()
                .build();

        // 4. 通过调度器将触发器与任务实例关联，保证按照触发器定义的条件执行任务
        stdScheduler.scheduleJob(jobDetail, trigger);

        // 5. 启动
        stdScheduler.start();
    }
}
```

运行主类，结果如下：

```shell
正在进行数据备份任务的定时执行，执行时间：2020-09-06:17:45:56
正在进行数据备份任务的定时执行，执行时间：2020-09-06:17:46:01
正在进行数据备份任务的定时执行，执行时间：2020-09-06:17:46:06
正在进行数据备份任务的定时执行，执行时间：2020-09-06:17:46:11
```

可以看到控制台果然每隔 5 秒就会有输出内容。

## 概念扩展

上面介绍过一些核心概念，还有一些概念也比较重要。

### JobExecutionContext 介绍

当 Scheduler 调用一个 Job 时，会将 `JobExecutionContext` 传递给 Job 的 `execute()` 方法。**Job 能通过 `JobExecutionContext` 对象访问到 Quartz 运行时的环境以及 Job 本身的详细数据。**

### JobDataMap 介绍

- 在进行任务调度时，JobDataMap 存储在 JobExecutionContext 中，非常方便获取；
- JobDataMap 可以用来装载任何可序列化的数据对象，当 Job 实例对象执行时，这些参数对象会传递给它；
- JobDataMap 实现了 JDK 的 Map 接口，并且添加了非常方便的方法用来存取基本数据类型；

主类：

```java
// 2. 任务实例 JobDetail
// 参数 1：任务的名称；参数 2：任务组的名称
JobDetail jobDetail = JobBuilder.newJob(QuartzHelloJob.class)
        .withIdentity("job1", "jobGroup1")
        .usingJobData("message", "job message") // 传参
        .build();
```

Job 实现类：

```java
// JobDataMap 传值
JobDataMap jobDataMap1 = jobExecutionContext.getJobDetail().getJobDataMap();
System.out.println("任务数据的参数值：" + jobDataMap1.get("message"));
```

还有一个用法，在 Job 实现类中添加 `setter` 方法对应 JobDataMap 的键，Quartz 框架默认的 jobFactory 实现类在初始化 Job 实例对象时会自动地调用这些 `setter` 方法。这样的好处是，你可以在 Job 实现类中方便地使用成员变量，而不用像上面那种方式去取值。

{% note warning no-icon %}
注意点：如果遇到同名的 key，Trigger 中的 key 对应的值会覆盖 JobDetail 中的值。
{% endnote %}

### 有状态 Job 和无状态的 Job

- 有状态 Job 可以理解为，多次 Job 调用期间可以持有一些状态信息，这些状态信息存储在 JobDataMap 中。
- 默认的无状态的 Job 每次调用时都会创建一个新的 JobDataMap。

`@PersisJobDataAfterExecution` 注解的使用在 Job 任务类上，多次 Job 实例被调用期间，可以持有一些状态信息，比如可以实现 count 的累加。

##  Trigger 介绍

Quartz 有一些不同的触发器类型，使用最多的是 `SimpleTrigger` 和 `CronTrigger`

- jobKey：表示 job 实例的标识，触发器被触发时，该指定的 job 实例被执行（也就是说，除了通过 JobDetail 获取到 job 实例的内容，Trigger 对象也提供了方法获取）；
- startTime：表示触发器的时间表，第一次开始被触发的时间，数据类型是 `java.util.Date`；
- endTime：表示触发器终止被触发的时间，数据类型也是 `java.util.Date`；

### SimpleTrigger 触发器

5 秒重复执行，只执行 3 次，设置结束时间：
```java
Trigger trigger = TriggerBuilder.newTrigger()
        .withIdentity("trigger1", "triggerGroup1")
        .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                .repeatSecondlyForever(5)
                .withRepeatCount(2))
        .usingJobData("message", "simple 触发器")
        .startNow()
        .endAt(endDate)
        .build();
```

- SimpleTrigger 属性有开始时间、结束时间、重复次数和重复时间间隔等
- 重复次数的属性值从 0 开始计数
- 重复时间间隔属性值必须是长整型的正整数
- 如果指定了结束时间属性值，那么，结束时间属性权重优于重复次数属性

### CronTrigger 触发器

CronTriggers 通常比 SimpleTrigger 更有用，因为它是基于日历的作业触发器。使用 CronTrigger，你可以指定诸如“每周五中午”或者“每个工作日的 9:30”这样的日程来触发 Job 执行。像 SimpleTrigger 一样，CronTrigger 也有一个 startTime 以指定日程从什么时候开始，也有一个（可选） endTime 以指定何时日程不再继续。

#### Cron Expressions —— Cron 表达式

Cron 表达式被用来配置 `CronTrigger` 实例。Cron 表达式是一个由 7 个表达式组成的字符串。每个子表达式描述一个单独的日程细节。这些表达式用空格分隔：
1. Seconds 秒
2. Minutes 分
3. Hours 小时
4. Day-Of-Month 月中的天，日
5. Month 月
6. Day-Of-Week 周中的天，周几
7. Year 年（可选）

| 字段       | 是否必填 | 允许值                                  | 可以出现的运算符                   |
| ---------- | -------- | --------------------------------------- | ---------------------------------- |
| 秒         | 必填     | 0 - 59                                  | /    *    ,   -                    |
| 分         | 必填     | 0 - 59                                  | /    *    ,   -                    |
| 时         | 必填     | 0 - 23                                  | /    *    ,   -                    |
| 月中的天   | 必填     | 1 - 31                                  | /    *    ,   -    ?  L    W    C  |
| 月         | 必填     | 1 - 12， 也可以用JAN这些缩写            | /    *    ,   -                    |
| 星期中的天 | 必填     | 1 - 7 或者 SUN-SAT，7是星期六， 可以用 WED 这些缩写 | /    *    ,   -     ?  L    C    # |
| 年份       | 非必填   | 1970   -   2099                         | /    *    ,   -                    |

> 1 表示周日

运算符的含义：

| 运算符 | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| *      | 表示这个字段的每一个值， 可以出现在所有字段中。 如”* * * * * ？*”表示每一秒钟。 |
| /      | 表示值的增量，从一个值开始，**每间隔**多少时间后再次执行，可以出现在所有的字段。如在分钟字段上， 0/15表示从0分开始， 间隔15分钟执行一次， 相当于0,15,30,45|
| ,      | 表示多个值， 可以出现在所有的字段。 如周一周三周五可表示为MON,WED,FRI。 |
| -      | 表示一个区间。如周一到周三可表示为MON-WED。                  |
| ?      | 表示放弃这个字段设置， 可以出现在月中的天和周中的天这两个字段。如”* * * * * ？*”表示每一秒钟。 |
| L      | L是 Last 的缩写， 可以出现在月中的天和周中的天这两个字段, 当时含义不一样。 在月中的天这个字段中， L表示这个月的最后一天。 而在周中的天中， 如果L单独出现， 那么就表示7或者SAT即星期六， 如果出现在一个数字的后面， 那么就表示这个月的周几， 如“6L”, 表示这个月的最后一个周五 |
| W      | W是 week 的缩写， 可以出现在月中的天这个字段， 可以用来指定距离这个时间最近的周几， 周的天在Day of Week字段中指定。 |
| #      | 表示这个月的第几个星期，可以出现在周中的天这个字段。 如 `1#3` 或 `SUN#3`， 表示这个月的第 3 个星期日。 |

> 月中的天和周中的天**互斥**，只能出现一个问号，例如，不能保证每周二都是 1 号！

{% note success no-icon %}
- `/` 这个符号还是挺常用的，比如在秒的位置，如果你直接 5，那么，就表示第 5 秒执行，但是如果是 `0/5` 则表示从 0 秒开始，每隔 5 秒！注意不要搞错！
- `L`  和 `W` 可以一起使用，比如每隔月最后一个周五结算工资
- 周字段的英文字母缩写不区分大小写，例如 `MON` 等价于 `mon`
- 在线 Cron 表达式生成器：https://www.matools.com/cron
{% endnote %}

Cron 表达式示例：
```shell
"0 0 10,14,16  * * ?" 每天 10 点、14 点、16 点执行
"0 0/30 9-17 * * ?" 每天 9-17 点，每隔 30 分钟执行一次
"0 0 12 ? * WED" 每周三的 12 点执行
"0 15 10 L * ?" 每月的最后一天的 10 点 15 分执行
"0 15 10 ? * 6#3" 每月的第三个周五 10 点 15 分执行 
```

#### CronTrigger 示例

```java
JobDetail jobDetail = JobBuilder.newJob(QuartzHelloJob.class)
        .withIdentity("job1", "jobGroup1")
        .usingJobData("message", "job message")
        .usingJobData("param", "setter 传参")
        .usingJobData("count", 1)
        .build();
```

## SchedulerFactory 

- 对于 Job 来说，Trigger 就类似于驱动器，没有触发器来定时驱动作业 Job，Job 就无法运行；
- 对于 Job 而言，一个 Job 可以对应多个 Trigger；
- 对于 Trigger 而言，一个 Trigger 只能对应一个 Job；

> Trigger 和 Job 因此就是「多对一」的关系

Scheduler 的创建方式：
（1） StdSchdulerFactory：Quartz 默认的 SchdulerFactory
- 使用一组参数（`java.util.Properties`）来创建和初始化 Quartz 调度器；
- 配置参数一般存储在 `quartz.properties` 文件中；
- 调用 `getScheduler` 方法就能创建和初始化调度器；

```java
StdSchedulerFactory stdSchedulerFactory = new StdSchedulerFactory();
Scheduler stdScheduler = stdSchedulerFactory.getScheduler();
// 等价于
Scheduler stdScheduler = StdSchedulerFactory.getDefaultScheduler();
```

### 将任务和触发器关联

```java
stdScheduler.scheduleJob(jobDetail, trigger);
```

### 其他用法
- 启动任务调度 `stdScheduler.start();`
- 调度挂起，即暂停（暂停后，需要再次调用 `start()` 方法才能启动）: `stdSchduler.standby();`
- 调度关闭（关闭之后，重新启动也不行）：`stdSchduler.shutdown();`

> shutdown() 方法可以传入一个布尔值，`boolean waitForJobsToComplete`，用于表示是否等待 Job 执行完毕再结束！ture 表示等待所有正在执行的 Job 执行完毕之后，再关闭 Scheduler。false 表示直接关闭 Scheduler。

## quartz.properties 配置

在项目中搜索该文件，可以在依赖的 Quartz 包下看到默认的配置文件：
```
# Default Properties file for use by StdSchedulerFactory
# to create a Quartz Scheduler Instance, if a different
# properties file is not explicitly specified.
#
# 调度器属性
## instanceName 用来区分特定调度器实例，可以按照功能用途来给调度器起名
org.quartz.scheduler.instanceName: DefaultQuartzScheduler
org.quartz.scheduler.rmi.export: false
org.quartz.scheduler.rmi.proxy: false
org.quartz.scheduler.wrapJobExecutionInUserTransaction: false

# 线程池属性
org.quartz.threadPool.class: org.quartz.simpl.SimpleThreadPool
## 处理 Job 的线程数，至少为 1，并不是越大越好
org.quartz.threadPool.threadCount: 10
## 线程的优先级，优先级别高的线程比级别低的线程优先得到执行。最小为 1，最大为 10，默认 5
org.quartz.threadPool.threadPriority: 5
org.quartz.threadPool.threadsInheritContextClassLoaderOfInitializingThread: true

# 作业存储设置
org.quartz.jobStore.misfireThreshold: 60000

org.quartz.jobStore.class: org.quartz.simpl.RAMJobStore
```

扩充：
- `org.quartz.scheduler.instanceId: AUTO`  这个值必须所有调度器实例中唯一，作为集群的唯一 key。
- `org.quartz.spi.ThreadPool.class` 一个实现了 `org.quartz.spi.ThreadPool` 接口的类，Quartz 自带的线程池实现类是 `org.quartz.smpl.SimpleThreadPool`

如果想自定义配置，那么只需要拷贝上面内容放在项目的 `resources` 目录下即可。也可以通过编写程序代码操作 `quartz.properties` 文件的内容：

```
// 通过代码方式配置 quartz
Properties properties = new Properties();
// 可以用工厂类中的常量
properties.put(StdSchedulerFactory.PROP_THREAD_POOL_CLASS, "org.quartz.simpl.SimpleThreadPool");
properties.put("org.quartz.threadPool.threadCount", "10");

// 1. 调度器 Schduler，从工厂中获取调度器实例
StdSchedulerFactory stdSchedulerFactory = new StdSchedulerFactory();
stdSchedulerFactory.initialize(properties);
Scheduler stdScheduler = stdSchedulerFactory.getScheduler();
```

> 通过 Properties 设置工厂属性的缺点在用硬编码，例如需要修改线程数量，将不得不修改代码，然后又重新编译。因此，不推荐使用。还是使用 `quartz.properties` 的配置方式方便。

## Quartz 监听器

### 概念

Quartz 监听器用于当任务调度你所关注的事件发生时，能够及时获取这一事件的通知。

Quartz 监听器主要有 `JobListener`、`TriggerListener`、`SchedulerListener` 三种。先明确两个概念：全局监听器与非全局监听器。二者区别在于：
- 全局监听器能够接收到所有的 Job/Trigger 的事件通知
- 非全局监听器只能接收到在其上注册的 Job 或 Trigger 事件，不在其上注册的 Job 或 Trigger 则不会进行监听。 

监听器的用法步骤大致如下：
- 新建一个类，实现监听器接口 `xxxListener`；
- 通过监听管理器创建并注册监听器

### JobListener

任务调度过程中，与任务 Job 相关事件包括：
- Job 开始要执行的提示；
- Job 执行完成的提示；

JobListener 接口包含几个重要的方法声明：
- `getName`：用于获取 JobListener 的名称；
- `jobToBeExecuted` 方法：Scheduler 在 JobDetail 将要被执行时调用该方法；
- `jobExecutedVetoed` 方法：Scheduler 在 JobDetail 即将被执行，但又被 TriggerListener 否决时调用该方法；
- `jobWasExecuted` 方法：Scheduler 在 JobDetail 被执行之后调用该方法；

### TriggerListener

TriggerListener 包含几个方法：
- `getName`：获取触发器的名称
- `triggerFired` 方法：当与监听器相关联的 Trigger 被触发，Job 上的 `execute()` 方法将被执行时，Scheduler 就调用该方法；
- `vetoJobExecution` 方法：在 Trigger 触发后，Job 将要被执行时由 Scheduler 调用这个方法。TriggerListener 给了一个选择去否决 Job 的执行。假如这个方法返回 true，这个 Job 将不会被此次 Trigger 触发而得到执行；
- `triggerMisfired` 方法：Scheduler 调用这个方法是在 Trigger 错过触发时。
- `triggerComplete` 方法：Trigger 被触发并且完成了 Job 的执行时，Scheduler 调用这个方法；

### SchedulerListener

SchedulerListener 会在 Scheduler 的生命周期中关键事件发生时被调用。与 Scheduler 有关的时间包括：
- 增加一个 Job/Trigger
- 删除一个 Job/Trigger
- Scheduler 发生错误
- 关闭 Scheduler 等

## 示例代码

完整示例代码：https://github.com/Michael728/java-middleware-demos/tree/master/quartz-demos

## 参考

- [B 站/任务调度 Quartz 视频教程全集（21P）| 3 小时从入门到精通](https://www.bilibili.com/video/BV1St411g72Z?p=5)
- [博客园/Quartz的基本使用之入门（2.3.0版本）](https://www.cnblogs.com/zhanghaoliang/p/7886110.html)