# Java 集合框架系统学习

## Java集合框架概述

Java 集合框架（Java Collections Framework）是 Java 编程语言中用于存储和操作数据集的一套类库。它提供了一套接口和实现类，用于处理不同类型的数据，如 List、Queue、Set 和 Map。
Java 集合可以看作是一种容器，用来存储各种对象数据。也可以看着是各种数据结构（List、Queue、Set、Map）的实现及各种衍生实现。

Java 中这些不同集合接口的区别：
- List：有序集合，可以包含重复元素
- Set：不能存储重复元素的集合，无序集合
- Queue：队列，用于按照特定顺序处理元素
- Map：键值对的集合，键不能重复，但值可以重复

与数组 array 区别：
- 数组的大小不可调整，数组的大小一旦定义就不能更改了。所以 java 集合中有动态数组 ArrayList
- 没有 Map 的功能，不能保存映射关系
  
## 集合类的层次结构

### 集合类概述

Java 集合类有 2 个根接口 Collection 和 Map，Collection 派生出三个子接口：List、Queue、Set。Map 是一个独立于 Collection 接口。所以 Java 集合框架共有 4 个上层接口。
Java 提供一些预定义的类和接口，Java 所有的集合类都位于 java.util 包下。
- Collection：List、Queue、Set
- Map：键值集合
