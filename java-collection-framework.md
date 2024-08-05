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

### Collection集合类层次结构图：List、Queue、Set

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


### ArrayList特性

ArrayList 一些特性，因为它底层数据结构是一个数组，所以它具备数组的特性：


- 根据数组索引访问数据元素数据访问效率高、可随机获取数据。
- 可动态扩容容量大小，数组 array 是固定大小。数据量大时扩容效率也低。
- 允许存储 Null 值。


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

### LinkedList特性

LinkedList 底层数据结构是一个双向链表结构，它具备链表的一些特性：


- 增加、删除数据元素效率高，比 ArrayList 增加、删除效率高。
  

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

## Map接口常用实现类

Map 接口是采用键值对 Map<K, V> 映射接口，保存 key 到 value 的映射，key 不允许重复，key 可以为 null。

Map 接口的方法集文档：
- https://docs.oracle.com/javase/8/docs/api/java/util/Map.html

Map 接口方法：
```Java
abstract void                 clear()
abstract boolean              containsKey(Object key)
abstract boolean              containsValue(Object value)
abstract Set<Entry<K, V>>     entrySet()
abstract boolean              equals(Object object)
abstract V                    get(Object key)
abstract int                  hashCode()
abstract boolean              isEmpty()
abstract Set<K>               keySet()
abstract V                    put(K key, V value)
abstract void                 putAll(Map<? extends K, ? extends V> map)
abstract V                    remove(Object key)
abstract int                  size()
abstract Collection<V>        values()
```

### HashMap概述

HashMap 类实现了 Map 接口。HashMap 底层数据结构是用 数组 + 链表 + 红黑树（JDK1.8） 三种数据结构结合实现的。 

> JDK1.8 中引入红黑树优化过长的链表。

HashMap增加数据元素时，会用哈希算法计算 key 的值，这个值就是哈希值，然后再把哈希值转换为数组的索引。HashMap 不会记录插入数据元素的顺序。

>HashFunc(key) -> 哈希值

添加数据时候出现冲突咋办？HashMap 采用链表法来解决冲突问题。当链表的长度大于阙值（阙值默认为 8）时，会将链表转化为红黑树。

HashMap 底层数据结构是 **数组 + 链表 + 红黑树（JDK1.8）**，示意图如下：

![image](https://github.com/user-attachments/assets/cee8a5bc-76ba-4ab9-9704-054ceedf6e08)

### HashMap特性

HashMap的底层数据结构较为复杂，由**数组+链表+红黑树**组成，所以具备这些数据结构的一些特性：
它有几个关键属性：初始容量，负载因子，阈值

- 插入、获取值的时间复杂度基本是 O(1)，能快速查找对应的值
- 允许 key 和 value 为 null
- HashMap 有一个初始容量和一个负载因子。初始容量是指哈希表的初始化容量大小，负载因子是指哈希表在扩容之前，可以存储的键值对数量与哈希表容量大小的比率。（默认的初始容量是 16，负载因子是 0.75）

- HashMap 是无序的，且顺序会不定时改变
- HashMap 是线程不安全的

如果需要线程同步，一种是使用 Collections 集合：

```Java
Map m = Collections.synchronizedMap(new HashMap(...));
```

还有一种并发Map - ConcurrentHashMap，它用分段锁实现线程安全。

HashTable 也是线程安全的， 不过它是一个遗留类，现在不应该使用它。
HashTable 除了 key 和 value 不能为 null，其它和 HashMap 基本相同，

### HashMap 方法

HashMap 方法文档：
- https://docs.oracle.com/javase/8/docs/api/java/util/HashMap.html Method Summary

![image](https://github.com/user-attachments/assets/406eeada-40cc-4d13-a686-bf7d52d99794)

代码例子：
```Java
// 引入 HashMap 类      
import java.util.*;

public class HashMapDemo {
    public static void main(String[] args) {
        HashMap<String, String> hashmap = new HashMap<String, String>();
        
        // 添加键值对
        hashmap.put("gg", "Google");
        hashmap.put("nametwo", "Tom");
        hashmap.put("nameone", "Jimmy");
       
        // 输出 key 和 value
        for (String k : hashmap.keySet()) {
            System.out.println("key: " + k + " value: " + hashmap.get(k));
        }
  
        for(String value: hashmap.values()) {
          // 输出每一个value
          System.out.println(value + ", ");
        }
		// 获取值 
		String val1 = hashmap.get("gg");
		// 修改值
		hashmap.put("gg", "baidu");
		String val2 = hashmap.get("gg");
		// 删除值
		hashmap.put("name3", "Leilei");
		hashmap.remove("gg")
		// 计算大小
		System.out.println("size: " + hashmap.size());
    }
}
```

## Set接口常用实现类

Set 不能重复，hashset，tree
