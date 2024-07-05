# Java集合框架学习：Java集合框架基础学习

## Java集合框架概述

Java 集合框架（Java Collections Framework）是 Java 编程语言中用于存储和操作一组对象数据的一套类库，集合提供了一种通用的方式来管理对象数据，能够让我们方便添加、删除、搜索和遍历元素。
它提供了一套接口和实现类，用于处理各种类型的集合数据，提供接口有 List、Queue、Set 和 Map。
Java 集合库可以看作是一种容器，用来存储和操作各种对象数据。
也可以看作是各种常用数据结构（List、Queue、Set、Map）的实现及各种衍生实现。

Java 中这些不同集合接口的区别：
- List：有序集合，可以包含重复元素
- Set：不能存储重复元素的集合，无序集合
- Queue：队列集合，用于按照特定顺序处理元素
- Map：键值对 key-value 的集合，键不能重复，但值可以重复

与数组 array 区别：
- 数组的大小不可调整，数组的大小一旦定义就不能更改了。所以 java 集合中有动态数组 ArrayList
- 没有 Map 的功能，不能保存映射关系
- 
## 集合框架类层次结构

### 集合类概述

Java 集合类有 2 个根接口 Collection 和 Map，Collection 派生出三个子接口：List、Queue、Set。Map 是一个独立于 Collection 的接口，它存储 K-V 值。
Java 提供一些预定义的类和接口，Java 所有的集合类都位于 java.util 包下。

### Collection 接口

[Oracle JavaSE8 文档 Collection 接口](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html)下的子接口和实现类截图：

![image](https://github.com/jiujuan/JavaHero/assets/45460739/b4d352fd-d26d-4a6b-b2de-1519a623ea1b)

从上面截图可以看出 Collection 接口上面还有一个 Superinterfaces 超级接口 `Iterable<E>`，迭代器接口；Collection 接口下面有很多子接口和很多实现类，下面介绍 Collection 接口下 3 个常用接口。

- Collection 根接口下常用的 3 个接口：

> **List** 接口：表示一个有序集合，集合中的元素都有一个对应的索引，允许有重复的元素，允许存储 null，可以根据索引访问指定元素。
> 
> **Queue** 接口：表示一个队列集合，先进先出的一种数据结构。
> 
> **Set** 接口：表示一个无序集合，不允许有重复的元素。

### Map 接口

[Oracle JavaSE8 文档 Map 接口](https://docs.oracle.com/javase/8/docs/api/java/util/Map.html)下的子接口和实现类截图：

![image](https://github.com/jiujuan/JavaHero/assets/45460739/fae34abe-45e0-4fea-80db-4ddcbc663166)

> **Map** 接口：表示键值对 K-V 的集合，key 到 value 的映射。

JDK 还为集合的各种操作提供了 2 个工具类：Collections 和 Arrays。

### 集合类层次结构

Collection 集合类的层次结构简图：
![image](https://github.com/jiujuan/JavaHero/assets/45460739/2a7955b1-4957-4113-a8a0-5f6fc03eb1f9)

Map 集合层次结构简图：
![image](https://github.com/jiujuan/JavaHero/assets/45460739/6b437585-4021-4173-832d-267273c522be)

Java 集合常用的实现类有：ArrayList、LinkedList、ArrayDeque、PriorityQueue、HashSet、LinkedHashSet、TreeSet、HashMap、TreeMap、HashTable。
