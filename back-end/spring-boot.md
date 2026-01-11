# Spring Boot 核心知识

## 快速入门

### 1. 项目创建

#### 1.1 使用Spring Initializr

- **在线创建**：访问 [Spring Initializr](https://start.spring.io/)，选择依赖生成项目
- **IDE创建**：使用IntelliJ IDEA或Eclipse的Spring Initializr插件创建
- **Maven命令创建**：

```bash
mvn archetype:generate -DgroupId=com.example -DartifactId=demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

#### 1.2 项目结构

```
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com
│   │   │       └── example
│   │   │           └── demo
│   │   │               ├── DemoApplication.java   # 主启动类
│   │   │               ├── controller            # 控制器层
│   │   │               ├── service               # 服务层
│   │   │               ├── repository            # 数据访问层
│   │   │               └── entity                # 实体类
│   │   └── resources
│   │       ├── application.properties          # 配置文件
│   │       ├── static                          # 静态资源
│   │       └── templates                       # 模板文件
│   └── test
│       └── java
│           └── com
│               └── example
│                   └── demo
└── pom.xml                                      # Maven配置
```

#### 1.3 主启动类

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

### 2. 核心注解

| 注解 | 描述 |
| :--- | :--- |
| `@SpringBootApplication` | 组合注解：`@Configuration` + `@EnableAutoConfiguration` + `@ComponentScan` |
| `@Configuration` | 标识配置类 |
| `@EnableAutoConfiguration` | 开启自动配置 |
| `@ComponentScan` | 开启组件扫描 |
| `@RestController` | 组合注解：`@Controller` + `@ResponseBody` |
| `@Controller` | 标识控制层组件 |
| `@Service` | 标识服务层组件 |
| `@Repository` | 标识数据访问层组件 |
| `@Component` | 通用组件注解 |
| `@Autowired` | 自动注入依赖 |
| `@Qualifier` | 限定自动注入的Bean |
| `@Value` | 注入配置值 |
| `@ConfigurationProperties` | 批量注入配置值 |
| `@Bean` | 声明Bean |
| `@Transactional` | 声明事务 |

### 3. 配置文件

#### 3.1 配置文件类型

- **application.properties**：
  ```properties
  server.port=8080
  spring.datasource.url=jdbc:mysql://localhost:3306/mydb
  spring.datasource.username=root
  spring.datasource.password=password
  ```

- **application.yml**（推荐）：
  ```yaml
  server:
    port: 8080
  
  spring:
    datasource:
      url: jdbc:mysql://localhost:3306/mydb
      username: root
      password: password
  ```

#### 3.2 多环境配置

```yaml
# application.yml
spring:
  profiles:
    active: dev

# application-dev.yml
server:
  port: 8080
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/dev_db

# application-prod.yml
server:
  port: 80
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/prod_db
```

#### 3.3 配置注入

```java
// 使用@Value注入单个配置
@Component
public class AppConfig {
    @Value("${server.port}")
    private int port;
    
    @Value("${spring.datasource.url}")
    private String dbUrl;
    
    // getter和setter
}

// 使用@ConfigurationProperties批量注入
@Component
@ConfigurationProperties(prefix = "spring.datasource")
public class DataSourceConfig {
    private String url;
    private String username;
    private String password;
    
    // getter和setter
}
```

## Web开发

### 1. RESTful API

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    // GET /api/users
    @GetMapping
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }
    
    // GET /api/users/{id}
    @GetMapping("/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        User user = userService.getUserById(id);
        if (user == null) {
            return ResponseEntity.notFound().build();
        }
        return ResponseEntity.ok(user);
    }
    
    // POST /api/users
    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody User user) {
        User savedUser = userService.saveUser(user);
        return ResponseEntity.created(URI.create("/api/users/" + savedUser.getId())).body(savedUser);
    }
    
    // PUT /api/users/{id}
    @PutMapping("/{id}")
    public ResponseEntity<User> updateUser(@PathVariable Long id, @RequestBody User user) {
        User existingUser = userService.getUserById(id);
        if (existingUser == null) {
            return ResponseEntity.notFound().build();
        }
        user.setId(id);
        User updatedUser = userService.saveUser(user);
        return ResponseEntity.ok(updatedUser);
    }
    
    // DELETE /api/users/{id}
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        User existingUser = userService.getUserById(id);
        if (existingUser == null) {
            return ResponseEntity.notFound().build();
        }
        userService.deleteUser(id);
        return ResponseEntity.noContent().build();
    }
}
```

### 2. 请求参数处理

```java
// 路径变量
@GetMapping("/users/{id}")
public User getUserById(@PathVariable Long id) {
    // ...
}

// 查询参数
@GetMapping("/users")
public List<User> getUsers(@RequestParam(required = false) String name, 
                           @RequestParam(defaultValue = "0") int page, 
                           @RequestParam(defaultValue = "10") int size) {
    // ...
}

// 请求体
@PostMapping("/users")
public User createUser(@RequestBody User user) {
    // ...
}

// 请求头
@GetMapping("/users")
public List<User> getUsers(@RequestHeader("Authorization") String token) {
    // ...
}

// Cookie
@GetMapping("/users")
public List<User> getUsers(@CookieValue("sessionId") String sessionId) {
    // ...
}
```

### 3. 异常处理

```java
// 全局异常处理
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleResourceNotFound(ResourceNotFoundException ex) {
        ErrorResponse error = new ErrorResponse("NOT_FOUND", ex.getMessage());
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationErrors(MethodArgumentNotValidException ex) {
        String errorMessage = ex.getBindingResult().getFieldErrors().stream()
                .map(error -> error.getField() + ": " + error.getDefaultMessage())
                .collect(Collectors.joining(", "));
        
        ErrorResponse error = new ErrorResponse("VALIDATION_ERROR", errorMessage);
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(error);
    }
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGenericException(Exception ex) {
        ErrorResponse error = new ErrorResponse("INTERNAL_SERVER_ERROR", "An unexpected error occurred");
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(error);
    }
}

// 自定义异常
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}

// 错误响应类
public class ErrorResponse {
    private String code;
    private String message;
    
    // 构造方法、getter和setter
}
```

## 数据访问

### 1. Spring Data JPA

#### 1.1 配置依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

#### 1.2 实体类

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(name = "username", nullable = false, unique = true)
    private String username;
    
    @Column(name = "email", nullable = false, unique = true)
    private String email;
    
    @Column(name = "password", nullable = false)
    private String password;
    
    @Column(name = "created_at")
    @CreationTimestamp
    private LocalDateTime createdAt;
    
    @Column(name = "updated_at")
    @UpdateTimestamp
    private LocalDateTime updatedAt;
    
    // getter和setter
}
```

#### 1.3 Repository接口

```java
public interface UserRepository extends JpaRepository<User, Long> {
    // 自动生成查询方法
    User findByUsername(String username);
    
    User findByEmail(String email);
    
    List<User> findByUsernameContaining(String keyword);
    
    // 使用@Query自定义查询
    @Query("SELECT u FROM User u WHERE u.createdAt > :date")
    List<User> findByCreatedAtAfter(@Param("date") LocalDateTime date);
    
    // 使用原生SQL
    @Query(value = "SELECT * FROM users WHERE username LIKE %:keyword%", nativeQuery = true)
    List<User> findUsersByKeyword(@Param("keyword") String keyword);
}
```

#### 1.4 Service层

```java
@Service
@Transactional
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }
    
    public User getUserById(Long id) {
        return userRepository.findById(id).orElseThrow(() -> new ResourceNotFoundException("User not found with id: " + id));
    }
    
    public User saveUser(User user) {
        return userRepository.save(user);
    }
    
    public void deleteUser(Long id) {
        userRepository.deleteById(id);
    }
    
    public User findByUsername(String username) {
        return userRepository.findByUsername(username);
    }
}
```

### 2. 事务管理

```java
// 类级别事务
@Service
@Transactional
public class UserService {
    // 所有方法都有事务
}

// 方法级别事务
@Service
public class UserService {
    
    @Transactional(readOnly = true)
    public User getUserById(Long id) {
        // 只读事务
    }
    
    @Transactional(propagation = Propagation.REQUIRED, isolation = Isolation.DEFAULT, timeout = 30)
    public User saveUser(User user) {
        // 事务配置
    }
}
```

## 测试

### 1. 单元测试

```java
@SpringBootTest
public class UserServiceTest {
    
    @MockBean
    private UserRepository userRepository;
    
    @Autowired
    private UserService userService;
    
    @Test
    public void testGetAllUsers() {
        // 准备测试数据
        List<User> users = Arrays.asList(
                new User("user1", "user1@example.com", "password1"),
                new User("user2", "user2@example.com", "password2")
        );
        
        // 模拟Repository方法
        when(userRepository.findAll()).thenReturn(users);
        
        // 执行测试
        List<User> result = userService.getAllUsers();
        
        // 验证结果
        assertEquals(2, result.size());
        verify(userRepository, times(1)).findAll();
    }
    
    @Test
    public void testGetUserById() {
        // 准备测试数据
        User user = new User("user1", "user1@example.com", "password1");
        user.setId(1L);
        
        // 模拟Repository方法
        when(userRepository.findById(1L)).thenReturn(Optional.of(user));
        
        // 执行测试
        User result = userService.getUserById(1L);
        
        // 验证结果
        assertNotNull(result);
        assertEquals("user1", result.getUsername());
        verify(userRepository, times(1)).findById(1L);
    }
}
```

### 2. API测试

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class UserControllerTest {
    
    @Autowired
    private TestRestTemplate restTemplate;
    
    @MockBean
    private UserService userService;
    
    @Test
    public void testGetAllUsers() {
        // 准备测试数据
        List<User> users = Arrays.asList(
                new User("user1", "user1@example.com", "password1"),
                new User("user2", "user2@example.com", "password2")
        );
        
        // 模拟Service方法
        when(userService.getAllUsers()).thenReturn(users);
        
        // 执行API请求
        ResponseEntity<List<User>> response = restTemplate.exchange(
                "/api/users",
                HttpMethod.GET,
                null,
                new ParameterizedTypeReference<List<User>>() {}
        );
        
        // 验证结果
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(2, response.getBody().size());
    }
}
```

## 常用功能

### 1. 日志

```yaml
# application.yml
logging:
  level:
    root: INFO
    com.example.demo: DEBUG
  file:
    name: logs/application.log
  pattern:
    console: "%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n"
    file: "%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n"
```

### 2. 缓存

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

```java
@SpringBootApplication
@EnableCaching
public class DemoApplication {
    // ...
}

@Service
public class UserService {
    
    @Cacheable(value = "users")
    public List<User> getAllUsers() {
        // 从数据库查询
    }
    
    @Cacheable(value = "users", key = "#id")
    public User getUserById(Long id) {
        // 从数据库查询
    }
    
    @CachePut(value = "users", key = "#user.id")
    public User saveUser(User user) {
        // 保存到数据库
        return user;
    }
    
    @CacheEvict(value = "users", key = "#id")
    public void deleteUser(Long id) {
        // 从数据库删除
    }
}
```

### 3. 定时任务

```java
@SpringBootApplication
@EnableScheduling
public class DemoApplication {
    // ...
}

@Component
public class ScheduledTasks {
    
    private static final Logger logger = LoggerFactory.getLogger(ScheduledTasks.class);
    
    // 每秒执行一次
    @Scheduled(fixedRate = 1000)
    public void reportCurrentTime() {
        logger.info("Current time: {}", LocalDateTime.now());
    }
    
    // 延迟5秒后，每10秒执行一次
    @Scheduled(fixedDelay = 10000, initialDelay = 5000)
    public void doSomething() {
        // 执行任务
    }
    
    // 每分钟的第30秒执行
    @Scheduled(cron = "30 * * * * ?")
    public void doSomethingWithCron() {
        // 执行任务
    }
}
```

## 打包与部署

### 1. 打包

```bash
# 打包成jar文件
mvn clean package

# 打包跳过测试
mvn clean package -DskipTests
```

### 2. 运行

```bash
# 运行jar文件
java -jar target/demo-0.0.1-SNAPSHOT.jar

# 指定配置文件运行
java -jar target/demo-0.0.1-SNAPSHOT.jar --spring.profiles.active=prod

# 指定端口运行
java -jar target/demo-0.0.1-SNAPSHOT.jar --server.port=8081
```

### 3. 部署方式

- **传统部署**：将jar文件上传到服务器，使用systemd或nohup运行
- **Docker部署**：
  ```dockerfile
  FROM openjdk:11-jre-slim
  VOLUME /tmp
  COPY target/demo-0.0.1-SNAPSHOT.jar app.jar
  ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
  ```
  
  ```bash
  docker build -t demo-app .
  docker run -p 8080:8080 demo-app
  ```

- **Kubernetes部署**：使用Deployment和Service部署到K8s集群

@author wujingkai