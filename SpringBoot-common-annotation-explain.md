# SpringBoot 框架中常用注解及使用说明

## 核心启动类注解

### @SpringBootApplication

作用：标记 SpringBoot 主启动类，组合了 `@Configuration`、`@EnableAutoConfiguration` 和 `@ComponentScan`，用于启动自动配置和组件扫描。

```java
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```
## Web层注解
### @RestController

作用：标记为 REST 控制器，等价于 `@Controller` + `@ResponseBody`，表示类中所有方法直接返回 JSON 数据而非视图。

```java
@RestController
@RequestMapping("/api")
public class UserController {
    @GetMapping("/user/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);
    }
}
// http://127.0.0.1:8080/api/user/{id}
```
### @RequestMapping

作用：映射 HTTP 请求到控制器方法，可以用于类级别和方法级别，指定 URL 路径
```java
@RestController
@RequestMapping("/api")
public class MyController {
    @GetMapping("/user/hello")
    public String hello() {
        return "Hello, world!";
    }
}
// http://localhost:8080/api/user/hello
```
### @GetMapping / @PostMapping / @PutMapping / @DeleteMapping

作用：简化 HTTP 方法映射，指定 HTTP 请求方法 GET/POST/PUT/DELETE。

 `@GetMapping` 等价于 `@RequestMapping(method=RequestMethod.GET)` 等。

```java
@GetMapping("/user/{id}")
public User getUser(@PathVariable Long id) {
    // 处理 GET 请求
}

@PostMapping("/user")
public User createUser(@RequestBody User user) {
    // 处理 POST 请求
}
```
### @RequestParam / @PathVariable

作用：分别从请求参数和 URL 路径中获取值。
`@RequestParam` 提取请求参数，映射到控制器方法的参数上。
`@PathVariable` 提取 URL 中的路径变量，映射到控制器方法的参数上。

```java
@RestController
@RequestMapping("/api")
public class MyController {
    @GetMapping("/user")
    public String getUser(@RequestParam("name") String name) {
        return "Hello, " + name + "!"; // name 来自请求参数，比如表单
    }
	
    @DeleteMapping("/user/{id}")
    public void deleteUser(@PathVariable("id") Long userId) {
    
     // URL 中的路径变量示例 
       return "User ID: " + userId;
    }

}
```

```java
@GetMapping("/user/{id}")
public User getUser(
    @PathVariable Long id,
    @RequestParam(required = false) String name
) {
    // id 来自路径，name 来自请求参数
}
```
## 依赖注入与 Bean 管理

### @Autowired

作用：自动注入装配 Spring 容器中的 Bean，常用于字段、构造方法或方法参数。

```java
@Service
public class UserService {
    public String getUsername() {
        return "Hello, Tom!";
    }
}

@RestController
@RequestMapping("/api")
public class MyController {
    @Autowired
    private UserService userService;

    @GetMapping("/user/name")
    public String username() {
        return userService.getUsername();
    }
}
```
### @Component / @Service / @Repository

作用：将类声明为 Spring 管理的 Bean，标记不同层的组件 - 分别是：`@Component` 通用组件、`@Service` 业务层、`@Repository` 数据持久层。

```java
@Service
public class UserService { /* ... */ }

@Repository
public class UserRepository { /* ... */ }

@Component
public class MyCustomComponent { /* ... */ }
```
### @Qualifier

作用：指定注入的 Bean 名称。

```java
@Service("myService")
public class MyService {
    // Service implementation
}

@RestController
public class MyController {
    @Autowired
    @Qualifier("myService")
    private MyService myService;
}
```
### @Conditional

作用：条件注解，根据条件决定是否创建 Bean。

```java
@Conditional(MyCondition.class)
@Bean
public MyBean myBean() {
    return new MyBean();
}

```
## 配置相关
### @Configuration & @Bean

作用：定义配置类，并在其中声明 Bean。

`@Configuration` 可替换 xml 配置文件，被注解的类包含一个或多个被 `@Bean` 注解的方法，这些方法将会被`AnnotationConfigApplicationContext`或`AnnotationConfigWebApplicationContext`类进行扫描，并用于构建 bean 定义，初始化 Spring 容器。

```java
@Configuration
public class AppConfig {
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```
### @Value

作用：注入配置文件（如 `application.properties`）中的属性值。

```java
@Component
public class MyComponent {
    @Value("${app.timeout:5000}") // 默认值5000
    private int timeout;
}
```
### @ConfigurationProperties

作用：批量绑定配置文件中的属性到对象。

```java
@ConfigurationProperties(prefix = "app")
public class AppConfig {
    private String name;
    private String version;
    // getters/setters
}
```
### @ConditionalOnProperty

作用：根据配置属性条件决定是否创建 Bean。

```java
@Bean
@ConditionalOnProperty(name = "cache.enabled", havingValue = "true")
public CacheService cacheService() { /* 仅当配置启用缓存时生效 */ }
```
## 其它

### @Aspect

作用：声明切面类，用于实现 AOP 操作（如日志、事务管理）。

```java
@Aspect
@Component
public class LogAspect {
    @Before("execution(* com.example.service.*.*(..))")
    public void logBefore(JoinPoint joinPoint) { /* 前置通知 */ }
}
```
### @ExceptionHandler

作用：处理控制器层的异常。

```java
@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleException(Exception e) {
        return ResponseEntity.status(500).body("Error: " + e.getMessage());
    }
}
```
### @Async

作用：标记异步方法（需配合 `@EnableAsync` 使用）。

```java
@Async
public void asyncTask() {
    // 异步执行
}
```
### @Transactional

作用：声明事务性方法。

```java
@Service
public class UserService {
    @Transactional
    public void beforeTransaction() {
        // Transactional operation
    }
}

```
### @Scheduled

作用：定义定时任务（需配合 `@EnableScheduling` 使用）。

```java
@Scheduled(fixedRate = 5000)
public void runTask() {
    // 每 5 秒执行一次
}
```