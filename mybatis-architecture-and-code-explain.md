# 标题：MyBatis - Java 数据库操作框架 ORM 框架 MyBatis 架构分析

## MyBatis 是什么

![image](https://github.com/user-attachments/assets/a4517618-6007-45cf-babf-3c7cbc57fef6)

MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。

MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。（from：[MyBatis 网站](mybatis.org/mybatis-3/zh_CN)）

## MyBatis 整体架构

### MyBatis 整体架构图

![image](https://github.com/user-attachments/assets/f959b630-00b0-4647-8530-a90559042b73)

   (来自：《MyBatis技术内幕》作者: 徐郡明)   


### 第一层：接口层

#### 接口层功能说明

 这一层的功能是提供应用程序、MyBatis 与数据库交互的 API，开发者可以通过这一层与 MyBatis 和数据库进行交互操作。接口层接收到调用请求时，会调用 MyBatis 核心处理层的相应模块来完成相应的操作。

![image](https://github.com/user-attachments/assets/c02d6a09-84b2-4289-87e0-b922a11b9aca)

#### 接口层 API 说明

 接口层的**几个核心 API 接口**：`SqlSession`、`SqlSessionFactory`、`SqlSessionFactoryBuilder`，还有 Mapper 接口。
 
 - **SqlSession**：核心接口，提供了 insert、update 等 CRUD 操作，提供了执行 SQL 命令、管理事物的方法等。
 - **SqlSessionFactory**：用于创建 `SqlSession` 的工厂类。
 - **SqlSessionFactoryBuilder**：用于构建 `SqlSessionFactory` 的构建器。

>用的是 JDBC 中的 Statement Id 和查询参数传给 SqlSession 对象，然后使用 SqlSession 对象完成与数据库的交互操作。

**Mapper 接口**：通过 `MapperProxy` 生成接口代理对象，将接口方法映射为 SQL 操作。

- Mapper 接口：定义操作数据库的方法，通常与具体的 SQL 语句一一映射对应。
- Mapper XML 文件：定义了 SQL 语句、SQL 中的参数，对应的 Mapper 接口的操作方法等。

> MyBatis 将配置文件中的每一个 `<mapper>` 节点抽象为一个 Mapper 接口，接口声明的方法与 `<mapper>` 节点中的 `<select>`、`<insert>`、`<update>`、`<delete>` 节点相对应，节点中 id 值为 Mapper 接口中的方法名，`parameterType` 值表示 Mapper 对应方法的入参类型，而`resultMap` 值则对应了 Mapper 接口表示的返回值类型或者返回结果集的元素类型。

![image](https://github.com/user-attachments/assets/a0ed1125-66fa-472e-9cb1-b2ef600b0439)

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

#### 日志模块

Mybatis 集成了多种日志框架

#### 数据源模块

MyBatis 不仅有自己数据源实现，还可以集成第三方数据源组件。

MyBatis 把数据源 DataSource 分为 3 种：
- UNPOOLED 不使用连接池的数据源
- POOLED 使用连接池的数据库
- JNDI 使用 JNDI 实现的数据源

在数据持久层中，连接池组件直接关系到持久层的性能表现。

#### 事务管理模块

MyBatis 对操作事务进行了抽象，其自身也提供了相应的事务接口和简单实现。它一般会与 Spring 事务框架集成。

#### 缓存模块

MyBatis 提供了一级缓存和二级缓存。

一级缓存，是会话级别的缓存，在 MyBatis 中每创建一个 SqlSession 对象，就表示启动一次会话，在这一次会话中，会在 Executor 对象中建立一个结果集缓存，将每次查询的结果缓存起来。在查询时，先查询一级缓存。

CachingExecutor 是 Executor 接口的装饰器，它为 Executor 对象增加了二级缓存的相关功能。

二级缓存是应用级别的缓存，它的生命周期与应用程序的生命周期相同。
与二级缓存相关的配置在 mybatis-config.xml 配置文件中，它是 `cacheEnabled` ，它是二级缓存的总开关，它的设置值为 `true` 时，二级缓存才生效。它的默认值为 true。

还有 2 个配置缓存的节点：

- 映射配置文件中配置 `<cache>`  或 `<cached-ref` 节点，解析时会为该配置文件指定的命名空间创建相应的 Cache 对象。用户可以通过 `<cache>` 节点的 type 属性自定义 Cache 对象
- `<select>` 节点的 useCache 属性，该属性表示对查询操作缓存到二级缓存中。

![image](https://github.com/user-attachments/assets/a116589c-6d80-4e72-86e1-2086dddfcc62)


## MyBatis 简单的执行流程

1. **加载、解析配置文件**

MyBatis 初始化时加载并解析配置文件

2. **构建 SqlSessionFactory**

解析完配置文件后，创建一个 `SqlSessionFactory` 实例，这是用于生成 `SqlSession`。

3. **创建 SqlSession**

`SqlSessionFactory` 负责创建 `SqlSession` 实例，`SqlSession` 是一个会话，提供了与数据库的会话功能，用于执行 SQL 相关的命令。

4. **执行Mapper**

`SqlSession` 提供了用于执行 SQL 的 API，包括通过 Mapper 接口操作数据库。Mapper接口是用户自定义的接口，里面定义了一些 SQL 操作方法，MyBatis 通过动态代理方式实现接口方法的调用。

5. **关闭 SqlSession**

完成数据库操作后，应该关闭 `SqlSession` 以释放资源。

## MyBatis 主要的代码接口和类说明

### 1. **接口层（API Layer）**

- **核心类**：
    
    - `org.apache.ibatis.session.SqlSession`：核心接口，提供执行 SQL 的方法。
    
    - `org.apache.ibatis.session.SqlSessionFactory`：用于创建 `SqlSession` 的工厂接口。
    
    - `org.apache.ibatis.session.SqlSessionFactoryBuilder`：用于构建 `SqlSessionFactory` 的构建器类。

### 2. **数据处理层（Data Handling Layer）**

- **核心类**：
    
    - `org.apache.ibatis.executor.Executor`：执行器接口，负责 SQL 的执行。
    
    - `org.apache.ibatis.executor.statement.StatementHandler`：负责处理 JDBC 的 `Statement`。
    
    - `org.apache.ibatis.executor.parameter.ParameterHandler`：负责参数处理。
    
    - `org.apache.ibatis.executor.resultset.ResultSetHandler`：负责结果集处理。

### 3. **配置层（Configuration Layer）**

- **核心类**：
    
    - `org.apache.ibatis.session.Configuration`：核心配置类，管理所有配置信息。
    
    - `org.apache.ibatis.mapping.MappedStatement`：封装 SQL 映射信息。
    
    - `org.apache.ibatis.type.TypeHandler`：类型处理器接口。
    
    - `org.apache.ibatis.plugin.Interceptor`：拦截器接口。


### 4. **映射层（Mapping Layer）**

- **核心类**：
    
    - `org.apache.ibatis.mapping.ResultMap`：定义结果集与 Java 对象的映射关系。
    
    - `org.apache.ibatis.mapping.ResultMapping`：定义列与属性的映射关系。
    
    - `org.apache.ibatis.mapping.SqlSource`：负责生成 SQL 语句。
    
    - `org.apache.ibatis.mapping.BoundSql`：封装最终的 SQL 语句及其参数。

### 5. **数据源层（DataSource Layer）**

- **核心类**：
    
    - `javax.sql.DataSource`：数据源接口。
    
    - `org.apache.ibatis.transaction.TransactionFactory`：事务工厂接口。
    
    - `org.apache.ibatis.transaction.Transaction`：事务接口。

### 6. **缓存层（Cache Layer）**

- **核心类**：
    
    - `org.apache.ibatis.cache.Cache`：缓存接口。
    
    - `org.apache.ibatis.cache.impl.PerpetualCache`：基于内存的缓存实现。
    
    - `org.apache.ibatis.cache.decorators.LruCache`：基于 LRU 算法的缓存实现。
    
    - `org.apache.ibatis.cache.decorators.BlockingCache`：支持阻塞的缓存实现。

### 7. **日志层（Logging Layer）**

- **核心类**：
    
    - `org.apache.ibatis.logging.Log`：日志接口。
    
    - `org.apache.ibatis.logging.LogFactory`：日志工厂类。
    
    - 支持多种日志框架的实现，如 `org.apache.ibatis.logging.slf4j.Slf4jImpl`。

### 8. **插件层（Plugin Layer）**

- **核心类**：
    
    - `org.apache.ibatis.plugin.Interceptor`：拦截器接口。
    
    - `org.apache.ibatis.plugin.InterceptorChain`：拦截器链。

### 9. **事务管理层（Transaction Management Layer）**

- **核心类**：
    
    - `org.apache.ibatis.transaction.Transaction`：事务接口。
    
    - `org.apache.ibatis.transaction.jdbc.JdbcTransaction`：基于 JDBC 的事务实现。
    
    - `org.apache.ibatis.transaction.managed.ManagedTransaction`：由容器管理的事务实现。

### 10. **类型处理器层（Type Handler Layer）**

- **核心类**：
    
    - `org.apache.ibatis.type.TypeHandler`：类型处理器接口。
    
    - 内置实现类：
        
        - `org.apache.ibatis.type.StringTypeHandler`
        
        - `org.apache.ibatis.type.IntegerTypeHandler`
        
        - `org.apache.ibatis.type.DateTypeHandler` 等。

### 11. **SQL 解析层（SQL Parsing Layer）**

- **核心类**：
    
    - `org.apache.ibatis.mapping.SqlSource`：SQL 源接口。
    
    - `org.apache.ibatis.scripting.xmltags.DynamicSqlSource`：处理动态 SQL。
    
    - `org.apache.ibatis.scripting.defaults.RawSqlSource`：处理静态 SQL。

### 12. **异常处理层（Exception Handling Layer）**

- **核心类**：
    
    - `org.apache.ibatis.exceptions.PersistenceException`：MyBatis 根异常类。
    
    - `org.apache.ibatis.exceptions.IbatisException`（已弃用）。

### 13. **工具层（Utilities Layer）**

- **核心类**：
    
    - `org.apache.ibatis.reflection.Reflector`：反射工具类。
    
    - `org.apache.ibatis.reflection.MetaObject`：元对象工具类。
    
    - `org.apache.ibatis.reflection.factory.ObjectFactory`：对象工厂接口。
    
    - `org.apache.ibatis.reflection.wrapper.ObjectWrapperFactory`：对象包装工厂接口。
    

### 14. **扩展层（Extension Layer）**

- **核心类**：
    
    - `org.apache.ibatis.reflection.factory.ObjectFactory`：对象工厂接口。
    
    - `org.apache.ibatis.reflection.wrapper.ObjectWrapperFactory`：对象包装工厂接口。
    
    - `org.apache.ibatis.reflection.ReflectorFactory`：反射工厂接口。
## 参考

- mybatis.org/mybatis-3/zh_CN MyBatis 网站
- https://book.douban.com/subject/27087564/ MyBatis 技术内幕，作者: 徐郡明
- https://github.com/mybatis/mybatis-3/tree/3.4.x mybatis github源码地址
