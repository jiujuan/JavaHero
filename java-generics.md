# Java基础学习：Java泛型详细学习和代码例子

## 泛型介绍

泛型（generics），我觉得这个名字就已经体现出了 Java 泛型的意思。

泛，广泛的意思。在 Java 编程语言中，有 8 种基本的数据类型，基本的数据类型定义后就只能表示一种具体的类型。
而泛型，在 Java 编程语言中，定义泛型时，泛型可以代表多种数据类型，在使用时，却可以使用具体的某一种数据类型。使用类、接口或方法时由外部传入。
这种就叫 “**类型参数化**” 或 “**参数化类型**” （type parameter），也就是说不同数据类型也可以作为一种参数，而这种参数可以作为方法的变量。

在 Java 编程中，一种数据类型怎么表示不同的数据类型呢？用泛型表示。“不同数据类型”可以用泛型来表示。

最开始我们用 Object 来代表所有的基本数据类型，但是如果用到具体类型，如 String，必须手动写代码进行强类型转换。
不想强类型转换怎么办？可以使用泛型，不需要手动写代码强制转换，Java 编译器在编译时候就会对泛型代码做强类型检查，代码违反类型安全时发出告警。把转换和检查的工作交给了 Java 编译器，程序员就少写点代码。

```Java
// 没有泛型时
public class Box {
    private Object object;

    public void set(Object object) { 
	    this.object = object; 
    }
    public Object get() { 
	    return object; 
    }
}

// 有泛型时
public class Box<T> {
    // T stands for "Type"
    private T t;

    public void set(T t) { 
	    this.t = t; 
    }
    public T get() { 
	    return t; 
    }
}
```

> Object 是 Java 类库中的一个特殊类，它是所有类的父类。Java 允许把任何类型的对象赋值给 Object 类型的变量。当一个类被定义后，如果没有指定继承的父类，那么默认父类就是 Object 类。

> Java 泛型是 JDK 1.5 引入的。

## 泛型优点

泛型优点上面已经提到过，这里总结下：

- **编译时强类型检查**：泛型要求在使用时制指定实际数据类型，Java 编译器在编译时会对泛型代码做强类型检查，发现违反类型安全时会发出警告。
- **避免类型转换**：Java 泛型避免类型转换。
- **代码重用性、简洁**：泛型类和集合可以用于各种数据类型，无需重复编写代码。
- **代码灵活性**：泛型允许创建可灵活地处理不同类型数据的代码，提高了可扩展性和维护性。
### 避免类型转换例子

没有使用泛型 vs 使用泛型

```Java
// 没有使用泛型
List list = new ArrayList();
list.add("hello world");
String s = (String) list.get(0);
```
没有使用泛型时，放入 ArrayList 集合中的数据都是 Object，因为只有 Object 才能包含所有的数据类型。获取具体类型时，就需要对 Object 进行强制类型转换。

```Java
// 使用泛型
List<String> list = new ArrayList<String>();
list.add("hello world");
String s = list.get(0);
```
定义了一个泛型的 ArrayList 容器，实例化时指定了具体类型 String，限定了 ArrayList 只能容纳 String 类型的数据。如果你传入其它类型数据，编译器就会报错不能通过编译。
### 提高代码重用性，让代码更简洁例子

使用了泛型：
```Java
public static <T> void swap(T a, T b) {
	T temp = a;
	a = b;
	b = temp;
}
```

没有使用泛型，我们就需要写多个方法：
```Java
public static void swapFloat(float a, float b) {
	float temp = a;
	a = b;
	b = temp;
}

public static void swapInt(int a, int b) {
	int temp = a;
	a = b;
	b = temp;
}
```

在没有泛型前，表示多种不同数据类型用 Object，
```Java
public class Node{
	Object data;
    
    // 多种类型转换
}
```

有泛型后
```Java
public class Node<T> {
	T data;
	
	public Node(T data) {
		this.data = data;
	}
}
```
## 泛型缺点

- 性能开销：类型检查和擦除过程，给泛型带来一定的额外开销，但不会产生大的影响。
- 反射的挑战：泛型类型的变量在编译时就被擦除了，给基于反射的操作带来一定挑战。
## 泛型实现原理

Java  的泛型代码只存在于源代码中，在 Java 源代码编译后的字节码中，全部泛型就被替换为原来的类型了。在运行时阶段，泛型代码是没有的，这叫 **类型擦除**。
有的人也称为 **伪泛型**，Java 在语法上支持泛型，但在编译阶段，把泛型代码擦除了，将泛型的表示替换为具体类型，具体就是把尖括号中的内容替换为具体类型了。

```Java
List<String> iList= List.of("hello", "world"); 
System.out.println(iList.get(0));
```
javap 反编译后：
```Java
List var1 = List.of("hello", "world"); 
System.out.println((String)var1.get(0));
```
反编译后看代码，获取元素时进行了一个类型强制转换。

Java 编译器咋实现类型擦除：
- 用 Object 或 界定的数据类型替代泛型，因此，生成的字节码仅包含普通的类、接口和方法。
- 如果有必要，会在恰当的位置插入强制类型代码来确保类型安全。
- 在继承了泛型类或接口的类中，会插入桥接方法来保留多态性。
## 泛型的使用

在 Java 种，泛型可以用在类、方法、接口中，分别称为泛型类、泛型方法、泛型接口。
### 泛型基本语法

- **泛型类语法**：

> 访问修饰符 class 类名<限定类型变量名>{ }
```Java
public class GenericClass<T> {
	private T key;

	//泛型构造方法形参key的类型为T，T的类型由外部指定
    public GenericClass(T key) { 
        this.key = key;
    }

	//泛型方法 getKey 的返回值类型为T，T的类型由外部指定
    public T getKey(){ 
        return key;
    }
}
```

- **泛型接口**：

> 访问修饰符 interface 接口名<限定类型变量名>{ }
```Java
// 接口传入类型 T
public interface GenericInterface<T> {
	public T data();
}
```

- **泛型方法**：

> 访问修饰符 <代表泛型的变量> 返回值类型 方法名(参数){ }
```Java
// <T> 泛型的类型，方法持有一个类型 T
// T 尖括号后面的 T，表示方法返回值的数据类型，返回 T 类型
// t 传入的泛型参数，类型也是 T
public <T> T genericMethod(T t){ // 可以接收任意类型数据
	System.out.println(t); 
	return t; 
}
```

- 一个泛型接口较为完整例子，来自李兴华的《java开发实战经典》，
```Java
// 定义泛型接口
interface Info<T>{          
    public T getVar() ; // 定义抽象方法，抽象方法的返回值就是泛型类型  
}

// 定义泛型接口的子类
class InfoImpl<T> implements Info<T>{     
    private T var ;    // 定义属性  
    
    public InfoImpl(T var){ // 通过构造方法设置属性内容  
        this.setVar(var) ;    
    }  
    
    public void setVar(T var){  
        this.var = var ;  
    } 
     
    public T getVar(){  
        return this.var ;  
    }  
} 

public class GenericDemo{  
    public static void main(String arsg[]){  
        Info<String> i = null;        // 声明接口对象  
        i = new InfoImpl<String>("hello world") ;  // 通过子类实例化对象  
        System.out.println("content：" + i.getVar()) ;  
    }  
}  
```
### 泛型类型参数常用命名

按照惯用法，类型参数命名一般是单个大写字母。常用命名参数如下：

> **T** - Type
> **E** - Element (在集合框架中广泛使用)
> **K** - Key（键）
> **V** - Value（值）
   **N** - Number（数值类型）
   **？** - 表示不确定的 java 类型
   S, U, V 等- 第二、第三、第四种类型
### 泛型高级语法的讲解

#### 多个类型参数
拥有有多个类型参数，用逗号隔开，
```Java
public interface Pair<K, V> {
    public K getKey();
    public V getValue();
}

public class OrderedPair<K, V> implements Pair<K, V> {

    private K key;
    private V value;

    public OrderedPair(K key, V value) {
	this.key = key;
	this.value = value;
    }

    public K getKey()	{ return key; }
    public V getValue() { return value; }
}
```
使用：
```Java
Pair<String, Integer> p1 = new OrderedPair<String, Integer>("Even", 8);
Pair<String, String>  p2 = new OrderedPair<String, String>("hello", "world");
```

还可以用参数化类型（如 `List<String>`）替代类型化参数（即 `K` 或 `V`），
```Java
OrderedPair<String, Box<Integer>> p = new OrderedPair<>("primes", new Box<Integer>(...));
```
#### 泛型类型通配符

在泛型定义中，可以通过设置通配符接收有限定类型或不确定类型的对象，使用 `?` 表示通配符，代替具体类型参数。比如 `List<?>` ，逻辑上它是 `List<String>`、`List<Float>` 的所有类型。

- **无限定类型通配符（Unbounded Wildcard）**：也叫无界通配符，匹配任意类型通配符。比如 `List<?>`。
- **有限定类型通配符（Bounded Wildcard）**：使用 `extends` or `super` 和具体类或接口来限定通配符所能匹配的类型范围。
    - **上边界通配符**：泛型参数限制在本类型或它的子类型。比如这个 `<? extends Number>` 表示参数可以是 Number（上边界）类型或 Number 的子类类型。
    比如一个方法中接收的泛型对象中只能是数字（Byte、Short、Long、Integer、Float、Double），在定义方法参数时，可以这样定义 `<? extends Number>`， `public void myMethod(List<? extends Number> myList) {}`。
    - **下边界通配符**：泛型参数限制在本类型或父类型。如 `<? super Integer>`，表示参数可以是 Number 类型或 Number 的父类类型。

上边界通配符例子：
```Java
public class GenericsDemo {
    public static double sum(List<? extends Number> list) {
        double s = 0.0;
        for (Number n : list) {
            s += n.doubleValue();
        }
        return s;
    }

    public static void main(String[] args) {
        List<Integer> arr = Arrays.asList(1, 2, 3);
        System.out.println("sum = " + sum(arr));
    }
}
```

下边界通配符例子，来自李兴华的《java开发实战经典》：
```Java
class Info<T> {
    private T var ; // 定义泛型变量
    public void setVar(T var){
        this.var = var ;
    }
    public T getVar(){
        return this.var ;
    }
    public String toString(){ // 直接打印
        return this.var.toString() ;
    }
}
public class GenericsDemo {
    public static void main(String args[]){
        Info<String> i1 = new Info<String>() ;  // 声明String的泛型对象
        Info<Object> i2 = new Info<Object>() ;  // 声明Object的泛型对象
        i1.setVar("hello") ;
        i2.setVar(new Object()) ;
        fun(i1) ;
        fun(i2) ;
    }
    public static void fun(Info<? super String> temp){// 只能接收String或Object类型的泛型，String类的父类只有Object类
        System.out.print(temp + ", ") ;
    }
}
```

总结：
- `<?>` 无限制类型通配符
- `<? extends E>，extends` 关键字声明了泛型类型的上界，可以表示本类型，或此类型的子类
- `<? super E>，super` 关键字声明了类型的下界，可以表示本类型，或此类型的父类

#### 泛型类型参数上下界

在使用泛型时，可以为传入的泛型类型参数进行上下边界的限制。就是说传入参数的类型限制为某种类型的父类或子类。

把上文的 `?` 换成类型，那么就是具体类型边界，不是包含各种类型。

在泛型中，还可以对泛型类型参数进行上下边界的限制。
- **上边界限制** `extends` 关键字，语法 `<T extends E>`。也可以设置**多个边界类型**，语法 `<T extends E1 & E2 & E3>`。
- **下边界限制** `super` 关键字，语法 `<T super E>`。
- **无边界通配符**：把上面的 `T` 换成 `?`，就是无界通配符。

```Java
// 定义一个泛型接口
public interface Animal {
    void makeSound();
}

// 实现Animal接口的Dog类
class Dog implements Animal {
    public void makeSound() {
        System.out.println("Woof!");
    }
}

// 泛型类，其泛型参数T有上界限制，必须是Animal或其子类
public class AnimalShelter<T extends Animal> {
    private T animal;

    public AnimalShelter(T animal) {
        this.animal = animal;
    }

    public void takeCareOf() {
        animal.makeSound();
    }
}

public class GenericsDemo {
    public static void main(String[] args) {
        // 创建Dog实例
        Animal dog = new Dog();
        // 创建AnimalShelter实例，其泛型参数为Dog，Dog是Animal的子类
        AnimalShelter<Dog> shelter = new AnimalShelter<>(dog);
        shelter.takeCareOf();
    }
}
```
## 参考资料

- [《java开发实战经典》](https://book.douban.com/subject/3879823/)作者：李兴华
- https://docs.oracle.com/javase/tutorial/java/generics/index.html oracle java generics
- [《一文搞懂Java泛型，有可能搞不懂》](https://juejin.cn/post/7249913673215836218) 蜀山剑客李沐白
- [《Java编程的逻辑》](https://book.douban.com/subject/30133440/) 作者：马俊昌
