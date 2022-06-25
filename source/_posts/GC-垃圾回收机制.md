---
title: GC - 垃圾回收机制
date: 2019-02-24 18:15:04
categories: Java
tags: [Java 300集, GC]
---
两件事情：

- 发现无用的对象
- 回收占用的空间

<!--more-->

# 怎么发现？

## 引用计数法

- 每个对象都有计数
- 被引用时，计数+1
- 当计数为 0，对象被回收
- 缺点：对象互相引用，导致计数不为0

## 引用可达法

- 将所有引用关系作图
- 如果对象无法被到达，就进行回收

# 分代垃圾回收机制

对象分为三种状态：

- 年轻
- 年老
- 持久（不考虑）

JVM也将堆分为

- Eden
- Survivor 1
- Survivor 2
- Old

## 回收类型

- Minor GC：清理Eden和Survivor区域的无用对象，复制有用对象到Survivor1或者Survivor2（同一时间，1和2一个有对象，一个为空）
- Major GC：清理Old区域
- Full GC：清理Eden，Survivor和Old区域。影响性能。

## 回收过程：

- 当 Eden 满了，触发 Minor GC，使用survivor1
- Eden 满了，触发 Minor GC，将有用对象放到survivor2
- 被转移15次的对象，被放入Old
- 当Old达到一定比例，触发 Major GC
- Old满的时候，触发 Full GC