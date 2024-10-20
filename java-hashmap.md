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

**链表与节点**，数据结构源码，Node 它实现了 Map.Entry 接口：

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

结构图解：

![image](https://github.com/user-attachments/assets/745744dd-6e16-4c4d-bdbd-ab9ff73ad6bc)

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

> **说明**：在上面构造函数源码中，没有看到初始化数组 table。那数组 table（哈希表） 是在什么时候初始化呢？是在第一添加键值对调用 put 方法初始化的。（jdk1.8）
> jdk1.7及以前，创建 HashMap 对象时就会从构造方法生成一个默认大小为 16 的 Entry[] table 数组。

### 添加键值对的put方法

HashMap 中 put 方法

**图解流程**：

这里找到了美团技术写的关于 HashMap 的 put 方法执行流程图，这张图画出了 put 方法里操作的几个重要数据结构、逻辑流程：

![image](https://github.com/user-attachments/assets/801a16fc-5ed3-4e61-b6c0-a469096c1e3f)


（来自：美团技术团队  [Java 8系列之重新认识HashMap](https://tech.meituan.com/2016/06/24/java-hashmap.html)）


**put 方法源码分析**：

```Java
//https://github.com/zxiaofan/JDK/blob/master/JDK1.8/src/java/util/HashMap.java#L610
public V put(K key, V value) {
	return putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
			   boolean evict) {
	Node<K,V>[] tab; Node<K,V> p; int n, i;
	// 1. tab（table）为空则初始化数组table。resize()函数作用初始化table或扩容table。
	// (tab = table) == null，把table赋值给tab，然后判断tab是否为null，第一次肯定为null
	// (n = tab.length) == 0，把数组的长度赋值给 n，然后判断n是否等于0
	if ((tab = table) == null || (n = tab.length) == 0)
		n = (tab = resize()).length; // 进行数组初始化，并将初始化数组的长度赋值给n
		
	// 2. i=(n - 1) & hash 计算索引下标。
	// 如果 tab[i] 为 null，也就是 key 对应下标元素不存在则直接插入元素 Node
	if ((p = tab[i = (n - 1) & hash]) == null)
		tab[i] = newNode(hash, key, value, null);
	else {
		Node<K,V> e; K k;
		// 3. key 对应下标元素是否存在，是 则覆盖当前元素值 value
		if (p.hash == hash &&
			((k = p.key) == key || (key != null && key.equals(k))))
			e = p;
		// 4. 判断 p 元素类型是否为红黑树节点，是就调用putTreeVal插入元素	
		else if (p instanceof TreeNode)
			e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
		else {// 5. else执行链表操作逻辑
		
		    // 循环遍历链表判断链表中是否有重复的key
			for (int binCount = 0; ; ++binCount) {
			    
			    // e = p.next 获取下一个元素，然后判断e的值是否为null，等于null此时就到达了链表尾部，元素没找到就插入元素
				if ((e = p.next) == null) {
					p.next = newNode(hash, key, value, null); // 创建新节点插入元素到尾部
					// 节点添加完后，判断此时节点个数是否大于 TREEIFY_THRESHOLD=8 
					// binCount>=7
					if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
						treeifyBin(tab, hash); // 转换为红黑树
					break; // 跳出循环
				}
				
				// 链表中key已经存在则跳出循环
				if (e.hash == hash &&
					((k = e.key) == key || (key != null && key.equals(k))))
					break; // 跳出循环
					
			    // 与前面 e= p.next 结合遍历链表，添加新元素与当前节点不相等，继续查找下一个节点
				p = e;
			}
		}
		// 如果找到了重复的元素，就用新值替换旧值，最后返回旧值
		if (e != null) { // existing mapping for key
			V oldValue = e.value;
			if (!onlyIfAbsent || oldValue == null)
				e.value = value; // 新值替换旧值，e.value 表示旧值 value表示新值
			afterNodeAccess(e);
			return oldValue; // 返回旧值
		}
	}
	++modCount;
	// 6.判断超过threshold阙值就扩容
	if (++size > threshold)
		resize();
	afterNodeInsertion(evict);
	return null;
}
```

代码执行步骤说明，put 方法调用的是 putVal 方法：

> 1、如果数组table=null 或 table.length=0 ，那么调用 resize() 方法初始化table
> 
> 2、计算tab（table）的索引下标所处位置元素
> 
>     - 如果为 null
>        那么新建一个Node然后插入元素；
> 
>     - 如果不为 null
> 
>        3. 判断key所在的元素是否存在或key不为null且相等，那么覆盖原来的值
> 
>        4. 判断元素是否为红黑树节点，是的话，就调用putTreeVal方法插入元素
> 
>        5. else 那么就执行链表操作逻辑
> 
>           循环链表查找当前链表是否有重复key，a）没有找到key，就插入值 b）找到了有重复key，替换旧值
> 
> 6、最后判断table大小是否超过阙值，是否扩容


### hash方法(计算数组table索引) 

hash 函数源码：

```Java
// JDK 1.8
static final int hash(Object key) {
	int h;
	// 如果 key= null 那么哈希值为 0
	// 否则 key 取 h = hashCode() 值，
	// 最后让 h 值的高 16 位与 h 值进行异或运算
	return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

`(h = key.hashCode()) ^ (h >>> 16)` 计算出来的哈希值更加分散，减少哈希碰撞几率。


```Java
// JDK1.7
static int hash(int h) {
    int h = hashSeed;
        if (0 != h && k instanceof String) {
            return sun.misc.Hashing.stringHash32((String) k);
        }

    h ^= k.hashCode(); // 第一步：取hashCode值
    h ^= (h >>> 20) ^ (h >>> 12); 
    return h ^ (h >>> 7) ^ (h >>> 4);
}

//JDK1.7，JDK1.8 中没有这个方法
static int indexFor(int h, int length) { 
    return h & (length-1); //第三步 取模运算 ，计算对象保存在数组table哪个位置
}
```
上面的 put 方法里有一个片段代码，计算对象保存在数组table的哪个位置 (n - 1) & hash：
```Java
tab[i = (n - 1) & hash]) // 计算数组的索引位置

// 这里的 n 就是数组table的长度
```

> Hash 算法步骤：
> 1、取key的 hashCode 值
> 2、根据 hashcode 计算出hash值（高位异或运算）
> 
> 元素对象保存在数组table哪个位置：通过取模计算索引值， (table.length - 1) & hash

JDK1.8 相对 JDK1.7 优化了高位运算的算法，通过hashCode()的高16位异或低16位实现的：(h = k.hashCode()) ^ (h >>> 16)。这么做可以在数组table的length比较小的时候，也能保证考虑到高低Bit都参与到Hash的计算中，同时不会有太大的开销。
> 来自：美团技术团队  [Java 8系列之重新认识HashMap](https://tech.meituan.com/2016/06/24/java-hashmap.html)  图

n 为 数组table 的长度，下图中的下标指的是数组索引，

![image](https://github.com/user-attachments/assets/ff4f70bf-74e7-43c0-9e1a-76c4905488be)


### resize扩容

HashMap 的扩容机制是指 HashMap 调整容量大小

## 参考
- https://tech.meituan.com/2016/06/24/java-hashmap.html Java 8系列之重新认识HashMap - 美团技术团队
- https://github.com/zxiaofan/JDK/blob/master/JDK1.8 jdk1.8 java.util.HashMap.java 源码
- https://github.com/openjdk/jdk/blob/master/src/java.base/share/classes/java/util/HashMap.java openjdk java.util.HashMap.java
- https://docs.oracle.com/javase/8/docs/api/index.html?java/util/HashMap.html  HashMap Docs

