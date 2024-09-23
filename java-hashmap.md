# Java集合框架学习：HashMap 原理分析

## HashMap简介

HashMap 在 java 中是最常用的数据结构，它以 key-value 键值对形式存储数据元素，key 映射到 value 值，key 不能重复，通过 key 来获取值。它是非线程安全的。是 Java Collections Framework 里的一个类 。

比如存储一个员工名字和员工 ID，就可以使用 HashMap， ID 作为 key，名字作为 value 进行存储。

## HashMap数据结构示意图

HashMap 底层数据结构是一个复合数据结构：
>数组+链表（JDK1.7）
>
>数组+链表+红黑树（JDK1.8引入红黑树，解决链表过长导致查询性能下降）

HashMap数据结构示意图：

![image](https://github.com/user-attachments/assets/3ab7d3fb-04e0-4051-9b70-09ce1fef4496)

数组里的索引是怎么来的呢？通过下面公式计算出索引值
```java
index = hashCode(key) & (length-1)
```

## HashMap数据结构源码

>java JDK 1.8 
>
>java.util.HashMap.java

### HashMap 存储结构示意图

数组 + 链表 + 红黑树（JDK1.8 增加红黑树）

![image](https://github.com/user-attachments/assets/264496c1-b359-4032-8b02-33afb2427f02)


### HashMap数据结构字段源码

**数组**，存储元素为 Node：
```Java
/**
 * The table, initialized on first use, and resized as
 * necessary. When allocated, length is always a power of two（分配时，长度始终是 2 的幂）.
 * (We also tolerate length zero in some operations to allow
 * bootstrapping mechanics that are currently not needed.)
 */
transient Node<K,V>[] table; // 哈希桶数组


//数组默认值，空数组：
static final Entry<?, ?>[] EMPTY_TABLE = {};  // 数组默认值
transient Entry<K, V>[] table = (Entry<K, V>[]) EMPTY_TABLE;
```

**链表**，数据结构源码，Node 它实现了 Map.Entry 接口：

> Node 是 HashMap 的一个内部静态类。
```Java
/**
 * Basic hash bin node, used for most entries.  (See below for
 * TreeNode subclass, and in LinkedHashMap for its Entry subclass.)
 */
static class Node<K,V> implements Map.Entry<K,V> {
        final int hash; // 数组索引位子
        final K key;
        V value;
        Node<K,V> next; // 链表下一个节点

        Node(int hash, K key, V value, Node<K,V> next) { // 链表数据结构
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }
        
        public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }
        
    ... ...
}
```

**链表节点**，Entry 数据结构，它实现了 Map.Entry 接口：

> Entry 是 HashMap 的一个内部静态类。
```Java
static class Entry<K, V> implements Map.Entry<K, V> {  
        final K key;  
        V value;  
        Entry<K, V> next;  
        int hash;  
    }
```

**红黑树** TreeNode
```Java
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
        TreeNode<K,V> parent;  // red-black tree links
        TreeNode<K,V> left;
        TreeNode<K,V> right;
        TreeNode<K,V> prev;    // needed to unlink next upon deletion
        boolean red;
        TreeNode(int hash, K key, V val, Node<K,V> next) {
            super(hash, key, val, next);
        }
    ... ...
}
```
