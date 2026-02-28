# Java 快速参考

> Java 开发常用代码片段

## 基础语法

### 类定义

```java
public class User {
    private String name;
    private String email;
    
    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }
    
    public String getName() {
        return name;
    }
}
```

### 接口和抽象类

```java
// 接口
public interface UserRepository {
    User findById(Long id);
    List<User> findAll();
    User save(User user);
}

// 抽象类
public abstract class BaseService {
    protected Logger logger = LoggerFactory.getLogger(getClass());
}
```

---

## Spring Boot

### Controller

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @GetMapping
    public List<User> getAll() {
        return userService.getAll();
    }
    
    @GetMapping("/{id}")
    public User getById(@PathVariable Long id) {
        return userService.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));
    }
    
    @PostMapping
    public User create(@Valid @RequestBody UserCreateRequest request) {
        return userService.create(request);
    }
}
```

### Service

```java
@Service
@Transactional
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    @Cacheable("users")
    public User getUserById(Long id) {
        return userRepository.findById(id)
            .orElse(null);
    }
}
```

### Repository

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
    List<User> findByActiveTrue();
}
```

---

## JPA 实体

### Entity

```java
@Entity
@Table(name = "users")
public class User {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false)
    private String name;
    
    @Column(unique = true, nullable = false)
    private String email;
    
    @Column
    private Integer age;
    
    @CreationTimestamp
    @Column(nullable = false, updatable = false)
    private LocalDateTime createdAt;
}
```

---

## 验证

### Bean Validation

```java
public class UserCreateRequest {
    
    @NotBlank
    @Email
    private String email;
    
    @NotBlank
    @Size(min = 2, max = 50)
    private String name;
    
    @Pattern(regexp = "^[a-zA-Z0-9]{6,}$")
    private String password;
}
```

---

## 异常处理

### @ControllerAdvice

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleUserNotFound(
        UserNotFoundException ex
    ) {
        return ResponseEntity
            .status(HttpStatus.NOT_FOUND)
            .body(new ErrorResponse("USER_NOT_FOUND", ex.getMessage()));
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationException(
        MethodArgumentNotValidException ex
    ) {
        return ResponseEntity
            .status(HttpStatus.BAD_REQUEST)
            .body(new ErrorResponse("VALIDATION_ERROR", ex.getMessage()));
    }
}
```

---

## 日志

### SLF4J

```java
@Slf4j
@Service
public class UserService {
    
    @Transactional
    public User create(UserCreateRequest request) {
        log.info("创建用户: {}", request.getName());
        
        try {
            User user = new User(request.getName(), request.getEmail());
            return userRepository.save(user);
        } catch (Exception e) {
            log.error("创建用户失败: {}", e.getMessage(), e);
            throw new ServiceException("创建用户失败");
        }
    }
}
```

---

## 配置文件

### application.properties

```properties
# 服务器配置
server.port=8080

# 数据源配置
spring.datasource.url=jdbc:postgresql://localhost:5432/mydb
spring.datasource.username=postgres
spring.datasource.password=postgres
spring.datasource.driver-class-name=org.postgresql.Driver

# JPA
spring.jpa.show-sql=true
spring.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect

# 日志
logging.level.root=INFO
logging.level.com.example=DEBUG
```

---

## 完整示例：用户 CRUD

### Entity

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false)
    private String name;
    
    @Column(unique = true, nullable = false)
    private String email;
    
    @Column
    private Integer age;
}
```

### Repository

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long>
    List<User> findByAge(Integer age);
    Optional<User> findByEmail(String email);
}
```

### Service

```java
@Service
@Transactional
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    public User create(User user) {
        return userRepository.save(user);
    }
    
    public User findById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));
    }
}
```

### Controller

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @GetMapping("/{id}")
    public ResponseEntity<User> getById(@PathVariable Long id) {
        User user = userService.findById(id);
        return ResponseEntity.ok(user);
    }
    
    @PostMapping
    public User create(@Valid @RequestBody UserCreateRequest request) {
        User user = new User(request.getName(), request.getEmail());
        return userService.create(user);
    }
}
```

---

## 常用库推荐

| 库 | 用途 |
|------|------|
| **Spring Boot** | Web 框架 |
| **Spring Data JPA** | ORM |
| **Lombok** | 简化代码 |
| **SLF4J** | 日志 |
| **Hibernate Validator** | 验证 |
| **MapStruct** | 对象映射 |
| **JUnit 5** | 测试 |
| **Mockito** | Mock 测试 |

---

## AI 编码检查清单

- [ ] 遵循 Java 命名规范
- [ ] 使用驼峰命名法
-   - 类名：PascalCase
-   - 方法名：camelCase
-   - 常量：UPPER_SNAKE_CASE
- [ ] 类职责单一
- [ ] 使用接口和抽象类
-   - 接口定义契约
-   - 抽象类提供默认实现
- [ ] 使用依赖注入
-   - 构造器注入
- - @Autowired 字段注入
- [ ] 使用 Spring Boot
-   - @RestController 定义 REST API
-   - @Service 定义服务层
-   - @Repository 定义数据访问层
- [ ] 使用 @Transactional 事务管理
-   - 方法级事务
-   - @Transactional(readOnly = true)
- [ ] 使用 Spring Data JPA
-   - 接口继承 JpaRepository
-   - 方法名查询（findByEmail）
- - Optional 返回类型
- [ ] 正确处理异常
    - @ControllerAdvice + @ExceptionHandler
    - 自定义异常类
    - 全局异常处理
- [ ] 使用日志
    - @Slf4j 注解
    - 分级日志
    - 正确使用日志级别
    - DEBUG、INFO、WARN、ERROR
- [ ] 编写单元测试
    - JUnit 5 + Mockito
    | 使用 @MockBean
    | Mock 依赖
    | when/verify
- [ ] 遵循安全编码规范
    - 输入验证
    - 密码加密（BCrypt）
    - SQL 注入防护
    - 使用 PreparedStatement
- [ ] 避免过度设计
    - KISS 原则
    - YAGNI 原则
- [ ] 考虑性能
    - 使用缓存
    - 异步处理（@Async）
    - 数据库索引
    - 避免 N+1 查询
- [ ] 代码审查要点
    - 代码可读性
    - 注释清楚
    - 命名有意义
    - 避免魔法数字
    - 避免硬编码
