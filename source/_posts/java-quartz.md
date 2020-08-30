---
title: Java 定时任务框架 Quartz 入门
date: 2020-07-04 12:49:10
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

Quartz 官网地址：http://www.quartz-scheduler.org

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

### 任务 Job

Job 就是指你想要实现的任务类，每一个 Job 必须实现 `org.quartz.job` 接口，且只需要实现接口定义的 `execute()` 方法。

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

Scheduler 调度程序-任务执行计划表，只有安排进执行计划的任务 Job（通过 schduler.scheduleJob 方法安排进执行计划），当它预先定义的执行时间到了（任务触发 trigger），该任务才会执行。

- Job 我们预先定义的希望在未来时间能够被调度程序执行的任务类，我们可以自定义。
- JobDetail 使用 jobDetail 来定义定时任务的实例，jobDetail 实例是通过 JobBuilder 类创建的。
- JobDataMap 可以包含不限量的（序列化）的数据对象，在 job 实例执行的时候，可以使用其中的数据。JobDataMap 是 Java Map 接口的一个实现，额外增加了一些便于存取基本类型的数据方法。
- Trigger 触发器，Trigger 对象是用来触发执行 Job 的。当调度一个 Job 时，我们实例触发器然后调整它的属性来满足 Job 执行的条件。表明任务在什么时候会执行。定义了一个已经被安排的任务将会在什么时候执行的时间条件，比如每 2 秒执行一次。
- JobBuilder 用于声明一个任务实例，也可以定义关于该任务的详情，比如任务名、组名等。这个声明的实例将会作为一个实际执行的任务。
- TriggerBuilder 触发器创造器，用于创建触发器 trigger 实例。
- JobListener、TriggerListener、SchedulerListener 监听器，用于对组件的监听。

> 注意 Job 和 JobDetail 的区分，Job 是指我们具体的「任务类」。JobDetail 指通过 JobBuilder 类创建出的一个实例，它和 Job 类进行了绑定。

贴一个官网的示例：

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

## 参考

- [B 站/任务调度 Quartz 视频教程全集（21P）| 3 小时从入门到精通](https://www.bilibili.com/video/BV1St411g72Z?p=5)