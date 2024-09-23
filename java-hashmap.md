# Java集合框架学习：HashMap 原理分析

## HashMap简介

HashMap 在 java 中是最常用的数据结构，它以 key-value 键值对形式存储数据元素，key 映射到 value 值，key 不能重复，通过 key 来获取值。它是非线程安全的。是 Java Collections Framework 里的一个类 。

比如存储一个员工名字和员工 ID，就可以使用 HashMap， ID 作为 key，名字作为 value 进行存储。

## HashMap数据结构图

HashMap 底层数据结构是一个复合数据结构：
>数组+链表（JDK1.7）
>数组+链表+红黑树（JDK1.8引入红黑树，解决链表过长导致查询性能下降）

HashMap数据结构示意图：

![image](https://github.com/user-attachments/assets/3ab7d3fb-04e0-4051-9b70-09ce1fef4496)

数组里的索引是怎么来的呢？通过计算
```java
index = hashCode(key) & (length-1)
```
