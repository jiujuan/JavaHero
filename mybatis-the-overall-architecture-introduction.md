标题：MyBatis - Java 数据库操作框架 ORM 框架 MyBatis 架构分析

## MyBatis 是什么

![[mybatis-logo-img.png]]
MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。

MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。（from：[MyBatis 网站](mybatis.org/mybatis-3/zh_CN)）

## MyBatis 整体架构

### MyBatis 整体架构图
![[MyBatis-Arch-img.png]]
   (来自：《MyBatis技术内幕》作者: 徐郡明)   


### 第一层：接口层

#### 接口层功能说明

 这一层的功能是提供应用程序、MyBatis 与数据库交互的 API，开发者可以通过这一层与 MyBatis 和数据库进行交互操作。接口层接收到调用请求时，会调用 MyBatis 核心处理层的相应模块来完成相应的操作。
 ![[api-mybatis-db-img.png]]
#### 接口层 API 说明

 接口层的**几个核心 API 接口**：`SqlSession`、`SqlSessionFactory`、`SqlSessionFactoryBuilder`，还有 Mapper 接口。
 
 - **SqlSession**：核心接口，提供了 insert、update 等 CRUD 操作，提供了执行 SQL 命令、管理事物的方法等。
 - **SqlSessionFactory**：用于创建 `SqlSession` 的工厂类。
 - **SqlSessionFactoryBuilder**：用于构建 `SqlSessionFactory` 的构建器。

>用的是 JDBC 中的 Statement Id 和查询参数传给 SqlSession 对象，然后使用 SqlSession 对象完成与数据库的交互操作。

**Mapper 接口：通过 `MapperProxy` 生成接口代理对象，将接口方法映射为 SQL 操作。

- Mapper 接口：定义操作数据库的方法，通常与具体的 SQL 语句一一映射对应。
- Mapper XML 文件：定义了 SQL 语句、SQL 中的参数，对应的 Mapper 接口的操作方法等。

> MyBatis 将配置文件中的每一个 `<mapper>` 节点抽象为一个 Mapper 接口，接口声明的方法与 `<mapper>` 节点中的 `<select>`、`<insert>`、`<update>`、`<delete>` 节点相对应，节点中 id 值为 Mapper 接口中的方法名，`parameterType` 值表示 Mapper 对应方法的入参类型，而`resultMap` 值则对应了 Mapper 接口表示的返回值类型或者返回结果集的元素类型。

![[mybatis-mapper-xml-img.png]]
（Mapper 接口和配置文件 Mapper.xml 之间的关系）
### 第二层：核心处理层（数据处理层）

这一层的功能是负责 SQL 解析、SQL 执行、参数映射、结果集映射等核心流程。
MyBatis 的初始化以及完成一次数据库操作所涉及的全部流程。
#### 配置信息解析

在编写 MyBatis 程序时，有三个地方可以添加配置信息，分别是：
- **mybatis-config.xml** 配置文件，全局配置文件
- **xxxMapper.xml** 映射配置文件
- **Mapper 接口**中的注解信息

MyBatis 初始化过程中，会加载这些配置信息，解析后的配置信息会保存到 Configuration 对象中。

>比如：`<resultMap>` 节点会被解析为 ResultMap 对象；`<result>` 节点会被解析为 ResultMapping 对象。

MyBatis 初始化配置过程中，除了会读取 mybatis-config.xml 配置文件以及映射文件，还会加载配置文件指定的类，来处理类中的注解，创建配置对象。
初始化之后，可以通过 SqlSessionFactory 创建 SqlSession 对象并对数据库进行操作。

- Configuration：核心配置类，全局配置解析，管理配置解析。`session.Configuration`
- MappedStatement：封装 SQL 映射信息。`mapping.MappedStatement`
#### SQL 解析

要解析的 SQL 语句通常存储在 xxxMapper.xml 配置文件中或 xxxMapper 接口的注解信息中。

比如 BlogMapper.xml 配置文件 xml 配置：
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.mybatis.example.BlogMapper">
  <select id="selectBlog" resultType="Blog">
    select * from Blog where id = #{id}
  </select>
</mapper>
```

BlogMapper.java 中的 Java 注解来配置 SQL  语句：
```java
package org.mybatis.example;
public interface BlogMapper {
  @Select("SELECT * FROM blog WHERE id = #{id}")
  Blog selectBlog(int id);
}
```

**动态拼接 SQL 语句：**

MyBatis 提供多种动态 SQL 语句对应的节点，比如 `<where>` 节点、`<if>` 节点 等等，通过这些节点的组合，满足动态 SQL 语句组装的需求。

**参数映射：**

用户传入的参数，通过 scripting 模块来解析，把参数解析到配置文件中定义的动态 SQL 节点，然后形成可执行的 SQL 语句。它会处理 SQL 语句中的占位符，绑定用户传入的参数。

- SQL 解析，解析动态 SQL，比如 `XMLScriptBuilder` 类
- 完成 SQL 语句的参数绑定，比如 `ParameterHandler`
#### SQL 执行与结果集封装

SQL 语句的执行步骤由多个组件组成，几个重要的组件：

   - Executor：SQL 执行器，SQL 语句的执行，还有一级缓存和二级缓存，事务相关操心。如 `SimpleExecutor`、`ReuseExecutor`、`BatchExecutor` 等。
   - StatementHandler：封装了 JDBC 的 `Statement` 对象，包括参数的设置和结果集的处理。
   - ParameterHandler：它完成 SQL 语句的参数绑定
   - ResultSetHandler：结果处理器，完成结果集的映射。返回的结果集转换成 `List<E>` 列表
   - MapperProxyFactory：动态代理 Mapper 接口
### 第三层：基础支持层
#### 反射模块

MyBatis 对 Java 原生反射进行了封装，提供了更加易用的 API。
- `reflection.Reflector`
#### 类型转换模块

类型转换模块功能：实现 JDBC 类型与 Java 类型之间互相转换。
在 `PreparedStatement` 为 SQ L语句绑定参数时，需要从 Java 类型转换成 JDBC 类型。
从结果集中获取数据时，则需要从 JDBC 类型转换成 Java 类型。

## 参考

- mybatis.org/mybatis-3/zh_CN MyBatis 网站
- https://book.douban.com/subject/27087564/ MyBatis 技术内幕，作者: 徐郡明
