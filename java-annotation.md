## 一、什么是元数据

注解，Annotations，在 Java 编程语言中它是一种元数据，它是元编程的一种。

什么是元数据？
> 元数据是描述数据的数据。
> 具体在 Java 中来说，就是描述代码与代码之间的关系，或代码与数据源之间的数据关系，代码与其它资源之间的关系。

元数据作用？
> 在 Java 中，元数据不影响程序代码本身的编译和执行，它用来生成其它文件（比如Java Doc）、在编译时生成代码、在程序运行时知道被运行代码的描述信息。

Java 最开始时，比如 struts 就是用 XML 格式来描述，hibernate 就是 hbm 文件来描述。
后来，基于元数据的广泛应用，JDK 在 1.5 及以后版本中就引入了 Annotation （注解）的概念来描述元数据。

## 二、什么是注解

### 什么是注解

Java 在 JDK 1.5 及以后版本中引入 Annotation（注解）特性，它与类、接口、枚举是处在同一个层次的类型。

> Java Annotation（注解）用 Java 代码来描述元数据。

有时也叫**标注**，就是用一些规定的特殊字符（比如自定义注解）在 Java 代码上打上一个标记，做一个记号，这个记号在程序编译或运行时会起到一些特殊作用。
后面，javac 编译器、或其它程序就可以通过反射来了解你的类或各元素上是否有标记，有何种标记，根据标记就可以解析出来然后做一些事情。

注解的作用：
>它可以用来生成文档、代码分析、编译检查和编译时生成代码。
>
>- 生成文档：通过代码里标识的注解生成文档。如 @param，@return 等。
>- 运行时或编译时动态处理：通过代码里标识的注解来进行代码分析、代码生成或数据处理。比如运行时使用反射（[这篇文章讲反射](https://www.cnblogs.com/jiujuan/p/16659488.html)）。
>- 编译检查：注解让编译器实现基本的编译时检查。如 @Override。

注解就像我们平时用的便利签，对某件事做一个标记。在 Java 里，就是对程序做标记，程序在编译或运行时这个 “便利签” 会起到作用。

![image](https://img2024.cnblogs.com/blog/650581/202405/650581-20240513223418527-185221381.png)

### 注解放在代码哪个位置

Java 代码中的包、类、构造方法、方法、成员变量、参数都可以用注解来修饰。

### 注解的处理时机

Java 处理注解：
- 编译时（Compile time）处理注解，这叫编译时注解。
- 运行时（Runtime）通过反射机制来处理注解，这叫运行时注解。

## 三、注解分类

### Java 注解分类

![image](https://img2024.cnblogs.com/blog/650581/202405/650581-20240513223126809-1299406571.png)

### 元注解

什么是元注解？就是注解的注解。给自定义注解添加注解，可以限定自定义注解作用范围。
Java 中的元注解：
- `@Target`
- `@Retention`
- `@Documented`
- `@Inherited`
- `@Repeatable`（JDK1.8 引入）

java.lang.annotation.Annotation 是所有注解类型的通用接口， 元注解与它的关系图：
![image](https://img2024.cnblogs.com/blog/650581/202405/650581-20240516183633073-1730329444.png)


#### @Target

>@Target：用来标记其它注解，限制此注解修饰的元素类型，也就是被`@Target`修饰的注解适用范围。如类、方法、字段等。

@Target 源码：

```Java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Target {
    ElementType[] value();
}
```
`ElementType` 是一个枚举类型，它定义了被 `@Target` 标记的注解可应用的范围：
```Java
public enum ElementType {
 
   TYPE, // 标记的注解可以应用于类的任何元素：类、接口、枚举类
 
   FIELD, // 标记注解可应用于成员变量（包括：枚举常量）
 
   METHOD, // 标记注解可应用于成员方法
 
   PARAMETER, // 标记注解可应用于方法参数
 
   CONSTRUCTOR, // 标记注解可应用于构造方法
 
   LOCAL_VARIABLE, // 局部变量，标记的注解可以应用于局部变量
 
   ANNOTATION_TYPE, // 注解类，标记的注解可以应用于注解类型
 
   PACKAGE, // 标记注解可应用于修饰：包
 
   TYPE_PARAMETER, // 标记注解可应用于类型参数，JDK 1.8 新增
 
   TYPE_USE // 此类型包括类型声明和类型参数声明，是为了方便设计者进行类型检查，JDK 1.8 新增

}
```

- @Target 使用
```
@Target(ElementType.TYPE) —— 接口、类、枚举、注解
@Target(ElementType.FIELD) —— 字段、枚举的常量
@Target(ElementType.METHOD) —— 方法
@Target(ElementType.PARAMETER) —— 方法参数
@Target(ElementType.CONSTRUCTOR) —— 构造函数
@Target(ElementType.LOCAL_VARIABLE) —— 局部变量
@Target(ElementType.ANNOTATION_TYPE) —— 注解
@Target(ElementType.TYPE_PARAMETER) —— 类型参数，可以应用于类的泛型声明之处
@Target(ElementType.PACKAGE) —— 包
@Target(ElementType.TYPE_USE)—— 此类型包括类型声明和类型参数声明
```

示例：
```Java
@Target(ElementType.TYPE)
public @interface TableName {
    public String tableName() default "className";
}
```
#### @Retention

>@Retention：在 Java 程序运行周期内，被 @Retention 标记的注解所支持的保留级别。一般都写RUNTIME。

@Retention 源码：
```Java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Retention {
    RetentionPolicy value();
}

public enum RetentionPolicy {
    SOURCE,   
    CLASS,     
    RUNTIME
}
```
- `RetentionPolicy.SOURCE` - 标记的注解仅在源文件中有效，编译器编译时会忽略。
- `RetentionPolicy.CLASS` - 默认策略，标记的注解在 class 文件中有效，但在运行时，不会被 JVM 保留。
- `RetentionPolicy.RUNTIME` - 标记的注解不仅会在 class 文件中存在，还在运行时有效。

示例：声明一个 Country 注解
```Java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface Country {
    //国家名称
    String name() default "defaultName";
    //国家语言
    String[] languages();
}
```
#### @Documented

>@Documented：用 @Documented 注解标记的元素，Javadoc 工具会将此注解标记的注解信息包含在 javadoc 中。

例子：
```Java
package demo.javase.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.Inherited;

@Documented
@Inherited
public @interface Book {
    //书名
    String name();
    //作者
    String author();
}
```

```Java
package demo.javase.annotation;

@Book(
        name = "Spring in Action",
        author = "Craig Walls",
)
public class DocumentAnnotation {}
```
上面代码用 javadoc 工具可以生成 @Book 注解信息。
#### @Inherited

>@Inherited：用 @Inherited 标记的注解将具有继承性。 如果某个类使用了 @Inherited 标记的注解，则其子类自动继承父类定义的注解。


例子，定义一个注解：
```Java
@Inherited
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD})
public @interface TestAnnotation {
    String[] values() default {"defaultValue", "One"};
    int number();
}
```
使用注解：
```Java
@TestAnnotation(values = {"Tom", "Jimmy"}, number = 10)
public class Person {
}

class Student extends Person{
	@Test
    public void test(){
        Class clazz = Student.class;
        Annotation[] annotations = clazz.getAnnotations();
        for (Annotation annotation : annotations) {
            System.out.println(annotation.toString());
        }
    }
}
```
上面示例中，即使 Student 类没有显示地被 @TestAnnotation 注解，但是它的父类 Person 被注解了，而且 @TestAnnotation 被 @Inherited 注解，因此 Student 类自动获得该注解的能力。
#### @Repeatable

> @Repeatable：允许在同一申明类型(类，属性，或方法)上多次使用同一个注解。

例子：
```Java
@Repeatable(Students.class)
public @interface Student {
     String role();
}

public @interface Students {
    Student[] value();
}

public class RepeatAnnotationDemo {
    @Student(role = "Midd")
    @Student(role = "Seni")
    public void doSomeThing(){ }
}
```

### JDK 内置注解

#### JDK 内置注解说明：

- @Override：用在类的方法上，标记该方法要覆写基类（包括接口、父类等）的方法。编译器会对标记的方法做签名检查。
```Java
// @Override 注解的定义
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {

}
```

- @Deprecated：可以用来标记类、成员变量、成员方法为过时的方法，编译器会对调用这些类、成员或方法时给出警告。

- @SuppressWarnings：用在类或方法上，强制忽略编译告警。
它的一些常见参数：
>- deprecation - 使用了不赞成使用的类或方法时的警告
>- unchecked - 执行了未检查的转换时的警告，例如当使用集合时没有用泛型 (Generics) 来指定集合保存的类型
>- fallthrough - 当 Switch 程序块直接通往下一种情况而没有 Break 时的警告
>- path - 在类路径、源文件路径等中有不存在的路径时的警告
>- serial - 当在可序列化的类上缺少 serialVersionUID 定义时的警告
>- finally - 任何 finally 子句不能正常完成时的警告
>- rawtypes - 在类参数上使用泛型时，rawtypes 会抑制与非特定类型相关的警告
>- all - 所有的警告
```Java
// 用法
@SuppressWarnings("")
@SuppressWarnings({})
@SuppressWarnings(value={})

@SuppressWarnings(value={"unchecked", "deprecation"}) 
等同于
@SuppressWarnings("unchecked", "deprecation")
```

- ＠SafeVarargs：jdk1.7 引入。用在方法或构造方法上，断言 varargs 参数（即可变长参数）会被安全地使用。
  >在声明具有模糊类型（比如：泛型）的可变参数的构造函数或方法时，Java编译器会报unchecked 警告。鉴于这些情况，如果程序员断定声明的构造函数和方法的主体不会对其varargs 参数执行潜在的不安全的操作，可使用 @SafeVarargs 进行标记，这样的话，Java 编译器就不会报 unchecked 警告。

- @FunctionalInterface 在 jdk1.8 引入，表示被修饰的接口是函数式接口。

简单示例：
```java
class AnnotationDemoOne{ 
    public void test() {
	    System.out.println("test one")
    } 
} 

class AnnotationDemoTwo extends AnnotationDemoOne {

	/* 重载父类的test方法 */ 
	@Override 
	public void test() { 
		System.out.println("test two")
	} 
	
	/* 被弃用的方法 */ 
	@Deprecated 
	public void oldMethod() { 
		System.out.println("Deprecated method")
	} 
	
	/* 忽略告警  */ 
	@SuppressWarnings("rawtypes") 
	public List processList() { 
	  List list = new ArrayList(); 
	  return list; 
	} 
}
```

### 自定义注解

#### 自定义注解

> 自定义注解，用 Java 给出的关键字 `@interface` 来定义，它也属于一种类型。就象在 Java 中定义类用 `Class` 关键字，只不过注解可以标记或修饰 Java 中各种元素。
> 注解默认继承了 `java.lang.annotation.Annotation` 接口。

用关键字 `@interface` 声明一个自定义注解，语法格式为：
```Java
[Access Modifier] @interface <Annotation name> { 
    <Type> <Method name>() [default value]; 
}
```

定义注解的一些规则：
- 方法的返回值不受限制，可以是任意类型
- 方法的默认返回值是可选的，可以有，也可以没有
- 定义注解时可以使用元注解
- 注解的方法不能有参数和异常签名（throws）

例子，定义一个注解：
```Java
// 定义一个简单的注解类型
public @interface DemoAnno {

}
```

```Java
// 定义一个复杂些的注解类型
@Target({ElementType.FIELD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
public @interface DemoAnno {}

例子说明：
>上面代码中定义了一个名为 `DemoAnno` 的注解，
>`@Target({ElementType.FIELD, ElementType.PARAMETER})` 表示 `DemoAnno` 可以在类成员或方法参数上修饰
>`@Retention(RetentionPolicy.RUNTIME)` 在运行时起作用

上面的注解 `DemoAnno` 是一个没有属性的注解。

#### 注解的属性

注解属性语法格式：
```Java
[访问级别修饰符] [数据类型] 名称() default 默认值;
```
说明：
- 访问修饰符只能是 `public` 或默认(不指定访问级别修饰符)
- 数据类型有限制，支持如下数据类型：
   - 所有基本数据类型（byte、char、short、int、long、float、double、boolean）
   - String 类型
   - enum 类型
   - Annotation 类型
   - Class 类
   - 以上所有类型数组
- default 可以设置默认值，也可以没有 default

> 如果注解中只有一个属性值，最好将属性名命名为 `value` 。因为，指定属性名为 `value`，在使用注解时，指定 value 的值时可以不指定属性名称。
> 如：`@TestPerson("Tom")` ，与  `@TestPerson(value = "Tom")` 效果相同。

示例：
```Java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface DemoAnnotation {
    String name() default "defaultName";
    public MethodTypeEnum type() default MethodTypeEnum.TYPE1;
}

public enum MethodTypeEnum {
    TYPE1, TYPE2
}
```
说明：
>上面代码中定义了一个名为 `DemoAnnotation` 的注解。
>`@Target(ElementType.METHOD)` 表示 `DemoAnnotation` 用来修饰方法的。
>`@Retention(RetentionPolicy.RUNTIME)` 表示在运行时起作用。
>`DemoAnnotation` 注解有 2 个属性，分别为 `name()` 和 `type()`，其中 `type()` 数据类型是 enum 类型。

## 四、注解处理器

自定义好了一种注解，怎么获取注解中的内容？怎么让自定义注解发挥作用？

### AnnotatedElement 接口说明

Java 使用 `java.lang.annotation.Annotation` 接口来代表元素上的注解，该接口是所有注解类型的父接口。除此之外，在 Java 中还有一个注解处理器接口 `java.lang.reflect.AnnotatedElement` ，它提供了一组方法，用于获取注解的信息。
![image](https://img2024.cnblogs.com/blog/650581/202405/650581-20240519173330949-322277097.png)

`AnnotatedElement` 接口代表当前在虚拟机中运行程序的注解元素。此接口允许以反射方式读取注解元素的信息，下列类都实现了该接口：
```java
java.lang.Class  
java.lang.Package  
java.lang.reflect.Field  
java.lang.reflect.Method  
java.lang.reflect.Constructor  
java.lang.reflect.Parameter
```
- `Class` - 类定义
- `Constructor` - 构造器定义
- `Field` - 类的成员变量定义
- `Method` - 类的方法定义
- `Package` - 类的包定义

`AnnotatedElement` 接口方法：

![image](https://img2024.cnblogs.com/blog/650581/202405/650581-20240519173412673-1906962991.png)

- `getAnnotation(Class<T> annotationClass)` ：返回程序元素上指定类型的注解，如果注解不存在，则返回 null。
- `getAnnotations()`：返回程序元素上存在的所有注解，如果注解不存在，则返回长度为 0 的数组。
- `getAnnotationsByType(Class<T> annotationClass)`：返回程序元素上指定类型的所有注解，包括重复的注解和继承的注解，如果注解不存在，则返回长度为 0 的数组。
- `isAnnotationPresent(Class<?extends Annotation> annotationClass)`：判断程序元素上是否存在某个指定类型的注解，存在则返回 true，否则返回 false。
- `getDeclaredAnnotation(Class<T> annotationClass)`：返回程序元素上指定类型的直接注解，如果注解不存在，则返回 null。此方法忽略父类的注解。
- `getDeclaredAnnotations()`：返回程序元素上指定类型的所有注解，如果注解不存在，则返回长度为 0 的数组。此方法忽略父类的注解。
- `getDeclaredAnnotationsByType(Class<T> annotationClass)`：返回程序元素上指定类型的所有注解，此方法忽略父类的注解。

### 几个例子

例子 1：
```Java
@Deprecated
public class MyClass {

}

@SuppressWarnings("unchecked")
public class MySubClass extends MyClass {

}

AnnotatedElement element = MySubClass.class;
Annotation[] annotations = element.getDeclaredAnnotations();
for (Annotation annotation : annotations) {
    System.out.println(annotation);
}

// 输出只包含 @SuppressWarnings，因为 @Deprecated 是从父类继承的
```

例子 2：
```Java
@Repeatable(MyAnnotations.class)
@interface MyAnnotation {
    String value();
}

@MyAnnotation("test1")
@MyAnnotation("test2")
public class MyClass {
}

AnnotatedElement element = MyClass.class;
MyAnnotation[] annotations = element.getAnnotationsByType(MyAnnotation.class);
for (MyAnnotation annotation : annotations) {
    System.out.println(annotation.value());
}

// 输出 "test1" 和 "test2"
```

例子 3：
```Java
import java.lang.annotation.Annotation;
import java.lang.reflect.AnnotatedElement;

@Deprecated
@SuppressWarnings("unchecked")
public class MyClass {
}

public class Main {
    public static void main(String[] args) {
        AnnotatedElement element = MyClass.class;

        // 检查 @Deprecated 注解是否存在
        if (element.isAnnotationPresent(Deprecated.class)) {
            System.out.println("@Deprecated annotation is present on MyClass.");
        }

        // 检查 @SuppressWarnings 注解是否存在
        if (element.isAnnotationPresent(SuppressWarnings.class)) {
            System.out.println("@SuppressWarnings annotation is present on MyClass.");
        }

        // 检查一个不存在的注解
        if (!element.isAnnotationPresent(Override.class)) {
            System.out.println("@Override annotation is not present on MyClass.");
        }
    }
}

/* 
output：

@Deprecated annotation is present on MyClass. 
@SuppressWarnings annotation is present on MyClass. 
@Override annotation is not present on MyClass.

*/

```

综合例子 4:
```Java
import java.lang.annotation.*;
import java.lang.reflect.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Repeatable(MyAnnotations.class)
@interface MyAnnotation {
    String value();
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface MyAnnotations {
    MyAnnotation[] value();
}

@MyAnnotation("annotation1")
@MyAnnotation("annotation2")
@Deprecated
@SuppressWarnings("unchecked")
public class ExampleClass {
}

public class AnnotatedElementExample {
    public static void main(String[] args) {
        AnnotatedElement element = ExampleClass.class;

        // getAnnotation
        Deprecated deprecated = element.getAnnotation(Deprecated.class);
        System.out.println("@Deprecated: " + (deprecated != null));

        // getAnnotations
        Annotation[] annotations = element.getAnnotations();
        System.out.println("All annotations:");
        for (Annotation annotation : annotations) {
            System.out.println(annotation);
        }

        // getDeclaredAnnotations
        Annotation[] declaredAnnotations = element.getDeclaredAnnotations();
        System.out.println("Declared annotations:");
        for (Annotation annotation : declaredAnnotations) {
            System.out.println(annotation);
        }

        // getAnnotationsByType
        MyAnnotation[] myAnnotations = element.getAnnotationsByType(MyAnnotation.class);
        System.out.println("MyAnnotation annotations:");
        for (MyAnnotation myAnnotation : myAnnotations) {
            System.out.println(myAnnotation.value());
        }

        // getDeclaredAnnotation
        SuppressWarnings suppressWarnings = element.getDeclaredAnnotation(SuppressWarnings.class);
        System.out.println("@SuppressWarnings: " + (suppressWarnings != null));

        // getDeclaredAnnotationsByType
        MyAnnotation[] declaredMyAnnotations = element.getDeclaredAnnotationsByType(MyAnnotation.class);
        System.out.println("Declared MyAnnotation annotations:");
        for (MyAnnotation myAnnotation : declaredMyAnnotations) {
            System.out.println(myAnnotation.value());
        }

        // isAnnotationPresent
        boolean isDeprecatedPresent = element.isAnnotationPresent(Deprecated.class);
        System.out.println("@Deprecated present: " + isDeprecatedPresent);
    }
}
```
## 五、参考

-  https://www.cnblogs.com/peida/archive/2013/04/24/3036689.html   熵减黑客，深入理解Java：注解（Annotation）自定义注解入门
-   https://www.cnblogs.com/springmorning/p/10261472.html 编程老司机，JavaSE基础：@Document元注解的使用
- https://docs.oracle.com/javase/tutorial/java/annotations/ Oracle 官方注解学习文档
- https://docs.oracle.com/javase/tutorial/java/annotations/predefined.html  java 内置注解

- https://docs.oracle.com/javase/8/docs/api/java/lang/annotation/Annotation.html java.lang.annotation.Annotation 接口 API 文档
- https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/AnnotatedElement.html java.lang.reflect.AnnotatedElement 接口 API 文档
- https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/AnnotatedElement.html#getAnnotations
-  https://www.cnblogs.com/jiujuan/p/16659488.html  java基础学习：java中的反射详解和使用实践
