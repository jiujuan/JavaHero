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
>- 代码分析：通过代码里标识的注解来进行代码分析或代码生成。一般使用反射（[这篇文章讲反射](https://www.cnblogs.com/jiujuan/p/16659488.html)）。
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
#### @Target

>@Target：用来标记其它注解，限制此注解修饰的元素类型，也就是被`@Target`修饰的注解可应用范围。

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

>@Documented：用 @Documented 注解标记的元素，Javadoc 工具会将此注解标记元素的注解信息包含在 javadoc 中。

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

>@Inherited：用 @Inherited 标记的注解将具有继承性。 如果某个类使用了 @Inherited 标记的注解，则其子类自动获得该注解。


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

## 参考

- java基础学习：java中的反射详解和使用实践 https://www.cnblogs.com/jiujuan/p/16659488.html
