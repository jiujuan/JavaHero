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

数组简单说明：

- 一个数组 table[]，类型是Node<K,V>（java1.8），在 java1.7中是 Entry<K,V>。这个数组有时也叫它哈希表、哈希数组
- 数组索引（下标），是键 key 经过 hash 函数处理后的值
- 数组容量（capacity），也就是 HashMap 的容量，有一个默认容量值
- 数组实际长度，也就是数组存储元素的实际数量（size）。这个实际数量到达一定值（threshold），数组容量会扩容

链表简单说明：
- 链表是为了解决 hash 值冲突问题。当通过键 key 计算出的 hash 值相同，会存储到数组的同一个位置，如果之前这个位置就有一个元素了，这时存储元素就起冲突了，怎么办？会将旧元素迁移到链表中，冲突的hash值的键值对（新元素）存储到数组中
- 链表节点用 Node<K,V>表示
- 链表元素数量，也就是链表长度

链表什么情况下转换为红黑树？
- 当链表元素数量超过 8 且数组长度超过 64 才会转换为红黑树。
- 如果当前数组(或哈希表)的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树

为什么引入红黑树：
- 链表长度过长时导致的查询性能下降

### HashMap数据结构字段源码

**数组**，存储元素为 Node：
```Java
/**
 * The table, initialized on first use, and resized as
 * necessary. When allocated, length is always a power of two（分配时，长度始终是 2 的幂）.
 * (We also tolerate length zero in some operations to allow
 * bootstrapping mechanics that are currently not needed.)
 */
//https://github.com/zxiaofan/JDK/blob/master/JDK1.8/src/java/util/HashMap.java#L395
transient Node<K,V>[] table; // 哈希桶数组，哈希表, jdk1.8


//数组默认值，空数组：
static final Entry<?, ?>[] EMPTY_TABLE = {};  // 数组默认值
transient Entry<K, V>[] table = (Entry<K, V>[]) EMPTY_TABLE; // jdk1.7
```

>Node[] table 的初始化长度length (默认值是16)。

**链表**，数据结构源码，Node 它实现了 Map.Entry 接口：

> Node 是 HashMap 的一个内部静态类。
```Java
//https://github.com/zxiaofan/JDK/blob/master/JDK1.8/src/java/util/HashMap.java
/**
 * Basic hash bin node, used for most entries.  (See below for
 * TreeNode subclass, and in LinkedHashMap for its Entry subclass.)
 */
static class Node<K,V> implements Map.Entry<K,V> {
        final int hash; // 数组索引位子
        final K key;
        V value;
        Node<K,V> next; // 链表下一个节点

        Node(int hash, K key, V value, Node<K,V> next) { // 链表数据结构，构造函数
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next; // 指向下一个节点 Node
        }
        
        public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }
        
    ... ...
}
```

newNode，创建一个 Node 节点：
```Java
// Create a regular (non-tree) node，创建一个Node节点（非树）
Node<K,V> newNode(int hash, K key, V value, Node<K,V> next) {
	return new Node<>(hash, key, value, next);
}
```

**链表节点**，Entry 数据结构，它实现了 Map.Entry 接口：

> Entry 是 HashMap 的一个内部静态类，它实现了 Map.Entry 接口。
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

创建 TreeNode：
```Java
// Create a tree bin node
TreeNode<K,V> newTreeNode(int hash, K key, V value, Node<K,V> next) {
	return new TreeNode<>(hash, key, value, next);
}
```
### HashMap类中重要属性：扩容阙值、负载因子、容量等

```Java
transient int size;  // hashmap 中包含的键值对的数量，哈希表实际存储元素数量

int threshold; // hashmap 发生扩容的阈值，等于 capacity * loadFactor。
//用来判断是否需要调整HashMap capacity大小。
//比如HashMap容量capacity为 16 x loadFactor为 0.75 = 12，threshold = 12，如果哈希表实际存储元素数量大于 12 就需要进行扩容。

final float loadFactor; // 哈希表扩容时使用的负载因子，默认为 0.75。

int DEFAULT_INITIAL_CAPACITY = 1 << 4; // HashMap 默认初始化容量大小 16，也就是数组（哈希表）容量大小是 16。
//这个初始化值可以通过构造函数修改。数组的容量必须是 2 的 n 次幂

final float DEFAULT_LOAD_FACTOR = 0.75f;   //负载因子默认值 0.75

int MAXIMUM_CAPACITY = 1 << 30; // HashMap 容量默认最大值，2的30次方。可以通过构造函数调整

final int TREEIFY_THRESHOLD = 8 // 链表转换为红黑树时链表的阙值（这里阙值就是链表的长度）
final int MIN_TREEIFY_CAPACITY = 64; // 链表转换为红黑树时哈希表的容量，大于 64 转换为红黑树
final int UNTREEIFY_THRESHOLD = 6; // 红黑树转换为链表的阙值，当一个红黑树中的元素数量少于 6 时，红黑树就转换为链表

```

## HashMap中的操作功能 API-方法

### 构造函数-初始化

- 无参初始化
- 有参数初始化，可以指定容量大小
```Java
// HashMap 使用时初始化
Map<Integer, String> map = new HashMap<>(); // 无参初始化

Map<Integer, String> map = new HashMap<>(10); // 有参初始化，指定容量大小
```

构造函数源码分析：
```Java
// 无参初始化
/**
 * Constructs an empty <tt>HashMap</tt> with the default initial capacity
 * (16) and the default load factor (0.75).
 */
public HashMap() {
    this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
}

// 有参初始化，初始化容量
/**
 * Constructs an empty <tt>HashMap</tt> with the specified initial
 * capacity and the default load factor (0.75).
 */
public HashMap(int initialCapacity) {
    this(initialCapacity, DEFAULT_LOAD_FACTOR);
}

// 有参初始化，初始化容量大小和负载因子
public HashMap(int initialCapacity, float loadFactor) {
	if (initialCapacity < 0)
		throw new IllegalArgumentException("Illegal initial capacity: " +
										   initialCapacity);
	if (initialCapacity > MAXIMUM_CAPACITY)
		initialCapacity = MAXIMUM_CAPACITY;
	if (loadFactor <= 0 || Float.isNaN(loadFactor))
		throw new IllegalArgumentException("Illegal load factor: " +
										   loadFactor);
	// 上面都是对参数进行效验
	// 参数赋值
	this.loadFactor = loadFactor; //负载因子
	this.threshold = tableSizeFor(initialCapacity); // 计算出合适的容量大小（2的倍数）
}

// 传入 Map<K,V> 参数，HashMap与这个Map键值对映射
public HashMap(Map<? extends K, ? extends V> m) {
	this.loadFactor = DEFAULT_LOAD_FACTOR;
	putMapEntries(m, false);
}

final void putMapEntries(Map<? extends K, ? extends V> m, boolean evict){... ...}
```

> **说明**：在构造函数源码中，没有看到初始化数组 table。那数组 table（哈希表） 是在什么时候初始化呢？是在第一添加键值对时，调用 put 方法初始化的。

### 添加键值对 put方法

put 方法源码：

```Java
//https://github.com/zxiaofan/JDK/blob/master/JDK1.8/src/java/util/HashMap.java#L610
public V put(K key, V value) {
	return putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
			   boolean evict) {
	Node<K,V>[] tab; Node<K,V> p; int n, i;
	// 1. tab（table）为空则创建数组table。也就是上面提到的数组table初始化在put方法里
	if ((tab = table) == null || (n = tab.length) == 0)
		n = (tab = resize()).length;
	if ((p = tab[i = (n - 1) & hash]) == null)
		tab[i] = newNode(hash, key, value, null);
	else {
		Node<K,V> e; K k;
		if (p.hash == hash &&
			((k = p.key) == key || (key != null && key.equals(k))))
			e = p;
		else if (p instanceof TreeNode)
			e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
		else {
			for (int binCount = 0; ; ++binCount) {
				if ((e = p.next) == null) {
					p.next = newNode(hash, key, value, null);
					if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
						treeifyBin(tab, hash);
					break;
				}
				if (e.hash == hash &&
					((k = e.key) == key || (key != null && key.equals(k))))
					break;
				p = e;
			}
		}
		if (e != null) { // existing mapping for key
			V oldValue = e.value;
			if (!onlyIfAbsent || oldValue == null)
				e.value = value;
			afterNodeAccess(e);
			return oldValue;
		}
	}
	++modCount;
	if (++size > threshold)
		resize();
	afterNodeInsertion(evict);
	return null;
}
```

## 参考
- https://tech.meituan.com/2016/06/24/java-hashmap.html Java 8系列之重新认识HashMap - 美团技术团队
- https://github.com/zxiaofan/JDK/blob/master/JDK1.8 jdk1.8 java.util.HashMap.java 源码
