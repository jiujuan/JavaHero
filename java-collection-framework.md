# Java集合框架学习：Java集合框架基础学习

## Java集合框架概述

Java 集合框架（Java Collections Framework）是 Java 编程语言中用于存储和操作一组对象数据的一套类库，集合提供了一种通用的方式来管理对象数据，能够让我们方便添加、删除、搜索和遍历元素。
它提供了一套接口和实现类，用于处理各种类型的集合数据，提供接口有 List、Queue、Set （这3接口属于Collection） 和 Map。
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

## List接口常用实现类

### ArrayList概述

ArrayList 实现了 List 接口，它是一个动态数组。
什么叫动态数组？第一它的底层数据结构是一个数组 array 实现，它是顺序存储；第二它可以动态扩容。每个 ArrayList 有一个 capacity 容量，表示底层数组的容量大小，默认值为10，ArrayList 容器内存储的元素个数（size）不能大于 capacity 容量，当增加数据元素个数 size 超过 capacity 容量时，ArrayList 容器会自动扩容容量大小。

![image](https://github.com/jiujuan/JavaHero/assets/45460739/35eb144a-c864-4296-9ef6-7403b8302bbb)


### ArrayList优缺点

ArrayList 一些特性，因为它底层数据结构是一个数组，所以它具备数组的特性：

**优点**：

- 根据数组索引访问数据元素数据访问效率高、可随机获取数据。
- 可动态扩容容量大小，数组 array 是固定大小。数据量大时扩容效率也低。
- 允许存储 Null 值。


**缺点**：

- 频繁增加、删除数据元素的效率低，因为要移动数据元素。数组 array 也有这个缺点。
- ArrayList 是线程不安全。
- 允许存储 Null 值，但要小心处理，以免后续操作中引发空指针异常。

### ArrayList方法

ArrayList 方法集文档：
- [https://docs.oracle.com/javase/8/docs/api/java/util/ArrayList.html](https://docs.oracle.com/javase/8/docs/api/java/util/ArrayList.html)。

下面是 ArrayList 部分方法截图：

![image](https://github.com/jiujuan/JavaHero/assets/45460739/92fb27f4-6b48-4f12-bb41-c64cc720efb6)


**ArrayList 方法使用例子**：
ArrayList 是一个动态数组，提供了添加、删除、修改、遍历、截取等功能。

```Java
import java.util.*;

public class ArrayListDemo {
    public static void main(String[] args) {
        List<String> list = new ArrayList<String>();

        // 增加
        list.add("苹果");
        list.add("apple");
        list.add("香蕉");
        list.add("orange");

        // 计算array size大小
        System.out.println(list.size());
		// 计算元素索引
        System.out.print(list.indexOf("apple"));
		// 截取
        System.out.println(list.subList(0, 2));

        // 遍历
        for (String str : list) {
            System.out.println(str);
        }

        // 迭代器遍历
        Iterator<String> ite = list.iterator();
        while (ite.hasNext()) {
            System.out.println(ite.next());
        }

        // 修改
        list.set(1, "橘子");

        // 删除
        //list.remove(0);

        // 排序
        ArrayList<String> names = new ArrayList<String>();
        names.add("Tom");
        names.add("Jimmy");
        names.add("Lily");
        Collections.sort(names);
        for (String name : names) {
            System.out.println(name);
        }
    }
}
```

### LinkedList概述

LinkedList 实现了 List 接口和 Deque 接口，它是一个顺序结构容器，也可以看作是一个队列 (Queue)，这个也可以当作栈 Stack 使用，Java 原先有个 Stack（栈） 类，官方已经不建议使用了。现在 Java 有了 ArrayDeque，它比 LinkedList 当作栈或队列使用时有更好的性能。

LinkedList 底层数据结构是一个双向链表，它的结构图如下：

![image](https://github.com/user-attachments/assets/0804d434-0138-491d-95f3-9aad380f29a2)

> **first**：指向第一个节点
> **last**：指向最后一个节点

### LinkedList优缺点

LinkedList 底层数据结构是一个双向链表结构，它具备链表的一些特性：

**优点**：
- 增加、删除数据元素效率高，比 ArrayList 增加、删除效率高。
**缺点**：
- 数据量大访问链表中间数据元素效率低，因为需要遍历链表查找元素。
- LinkedList 是线程不安全的。

如果多个线程同时访问一个链表，并且至少有一个线程在结构上修改了链表，则必须进行外部同步，可以用 `Collections.synchronizedList`：
```Java
List list = Collections.synchronizedList(new LinkedList(...));
```

### LinkedList方法

LinkedList 方法文档：
- [https://docs.oracle.com/javase/8/docs/api/java/util/LinkedList.html](https://docs.oracle.com/javase/8/docs/api/java/util/LinkedList.html)。

LinkedList 部分方法截图：

![image](https://github.com/user-attachments/assets/3dfddddf-0cd2-484b-bf44-e197f2c0e7f5)


**LinkedList 方法使用例子**：
```Java
import java.util.*;

public class LinkedListDemo {
    public static void main(String[] args) {
        LinkedList<String> names = new LinkedList<>();

        names.add("Tom");
        names.add("Jimmy");
        names.add("Lily");
        names.add("Lisa");

        // 指定位置插入元素
        names.add(3, "Tomosn");

        // 遍历
        for (int i=0; i<names.size(); i++) {
            System.out.println(names.get(i));
        }

        // for-each 迭代遍历
        for (String name : names) {
            System.out.println(name);
        }

        // 头部增加数据元素
        names.addFirst("Jojo");

        // 尾部增加数据元素
        names.addLast("Zozo");

        System.out.println(names);

        // 删除
        // names.remove(2);

        // 修改指定位置元素
        names.set(0, "lilei");

        //删除并返回列表中的第一个元素。
        // names.removeFirst();
    }
}
```


