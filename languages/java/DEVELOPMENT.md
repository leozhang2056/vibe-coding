# Java 编码规范

> 传统企业级开发语言规范

## 命名规范

### 驼峰命名法

```java
// ✅ 正确
class UserService {}
public void getUserName() {}
private static final int MAX_RETRY_COUNT = 3;
UserAccount currentUser;

// ❌ 错误（不推荐）
class userService {}
public void get_user_name() {}
private static final int MAX_RETRY_COUNT = 3;
User_Account current_user;
```

| 类型 | 规范 | 示例 |
|------|------|------|
| 类 | PascalCase | `UserService` |
| 方法 | camelCase | `getUserName` |
| 变量 | camelCase | `userName` |
| 常量 | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| 包名 | 小写点分隔 | `com.example.service` |

---

## 代码组织

### 推荐的包结构

```
com.example.project/
├── model/          # 实体类
├── repository/     # 数据访问层
├── service/        # 业务逻辑层
├── controller/     # 控制器层
├── util/           # 工具类
└── Application.java  # 入口
```

### 单一职责

```java
// ✅ 正确（职责单一）
public class UserService {
    public void createUser(User user) {
        validate(user);
        saveToDatabase(user);
        sendWelcomeEmail(user);
    }
}

// ❌ 错误（职责混乱）
public class UserService {
    public void createUser(User user) {
        saveToDatabase(user);
        sendEmail(createWelcomeEmail(user));
        updateLoggingTable(user);
        triggerNotification(user);
        // ...太多职责
    }
}
```

---

## 面向对象编程（OOP）

### 类设计原则

```java
// ✅ 正确（封装）
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

// ❌ 错误（字段直接暴露）
public class User {
    public String name;
    public String email;
}
```

### 接口和抽象类

```java
// 接口
public interface UserRepository {
    User findById(Long id);
    List<User> findAll();
    void save(User user);
}

// 抽象类
public abstract class BaseService {
    protected abstract Logger logger;
    
    protected void log(String message) {
        logger.info(message);
    }
}
```

---

## 异常处理

### 自定义异常

```java
// ✅ 正确
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(String userId) {
        super("用户不存在: " + userId);
    }
}

// 使用
throw new UserNotFoundException(userId);
```

### Try-With-Resources

```java
// ✅ 正确
try (Connection conn = dataSource.getConnection()) {
    // 使用连接
} catch (SQLException e) {
    logger.error("数据库连接失败", e);
    throw new DatabaseException("连接失败", e);
} finally {
    // 清理资源
}
```

---

## 集合框架

### Spring Boot 规范

```java
// Controller
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @GetMapping("/{id}")
    public ResponseEntity<User> getById(@PathVariable Long id) {
        return userService.findById(id)
            .map(user -> ResponseEntity.ok().body(user))
            .orElse(ResponseEntity.notFound().build());
    }
}

// Service
@Service
@Transactional
public class UserService {
    @Autowired
    private UserRepository userRepository;
    
    public User createUser(User user) {
        return userRepository.save(user);
    }
}
```

---

## 数据库访问

### Spring Data JPA

```java
// Repository 接口
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
    List<User> findByActiveTrue();
}

// 使用
Optional<User> user = userRepository.findByEmail("user@example.com");
user.ifPresent(u -> logger.info("用户已存在: {}", u.getName()));
```

---

## 并发编程

### 线程池

```java
@Configuration
@EnableAsync
public class AsyncConfig {
    
    @Bean
    public Executor taskExecutor() {
        return Executors.newFixedThreadPool(10);
    }
}

// 使用
@Service
public class EmailService {
    
    @Async
    public void sendEmail(String to, String subject, String body) {
        JavaMailSender.send(to, subject, body);
    }
}
```

---

## 设计模式

### 工厂模式

```java
public interface PaymentProcessor {
    PaymentResult process(PaymentRequest request);
}

public class PaymentProcessorFactory {
    private Map<PaymentType, PaymentProcessor> processors = new HashMap<>();
    
    public PaymentProcessor getProcessor(PaymentType type) {
        return processors.get(type);
    }
    
    public void registerProcessor(PaymentType type, PaymentProcessor processor) {
        processors.put(type, processor);
    }
}
```

### 单例模式

```java
// ✅ 线程安全
public enum DatabaseConnection {
    INSTANCE;
    
    private DataSource dataSource;
    
    public Connection getConnection() throws SQLException {
        return dataSource.getConnection();
    }
}
```

---

## 测试

### JUnit 5

```java
@SpringBootTest
class UserServiceTest {
    
    @Autowired
    private UserService userService;
    
    @MockBean
    private UserRepository userRepository;
    
    @Test
    void testCreateUser() {
        // Given
        User user = new User("张三", "zhangsan@example.com");
        
        when(userRepository.findByEmail("zhangsan@example.com"))
            .thenReturn(Optional.empty());
        
        // When
        User result = userService.createUser(user);
        
        // Then
        assertThat(result.getName()).isEqualTo("张三");
        verify(userRepository).save(any(User.class));
    }
}
```

---

## 性能优化

### 缓存

```java
@Cacheable("users")
public User getUserById(Long id) {
    return userRepository.findById(id)
        .orElse(null);
}

@CacheEvict(value = "users", allEntries = true)
public void refreshCache() {
    // 清除缓存
}
```

### 懒加载

```java
@SpringBootApplication
@EntityScan(basePackages = "com.example")
@EnableCaching
@EnableScheduling
public class Application {
    // 主启动类
}
```

---

## 日志

### SLF4J

```java
@Slf4j
@Service
public class UserService {
    
    public void createUser(User user) {
        log.info("创建用户: {}", user.getName());
        
        try {
            userRepository.save(user);
            log.debug("用户创建成功: {}", user.getId());
        } catch (Exception e) {
            log.error("创建用户失败", e);
            throw new ServiceException("创建用户失败");
        }
    }
}
```

---

## 安全

### 输入验证

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

### 密码加密

```java
@Service
public class UserService {
    
    @Autowired
    private PasswordEncoder passwordEncoder;
    
    public void createUser(User user) {
        // 加密密码
        String encoded = passwordEncoder.encode(user.getPassword());
        user.setPassword(encoded);
        
        userRepository.save(user);
    }
}
```

---

## 反模式（避免）

### 1. God Object

```java
// ❌ 反模式
class UserService {
    private DatabaseConnection db;
    private EmailService email;
    private FileLogger logger;
    private Translator translator;
    // ...太多职责
}
```

### 2. 静态方法滥用

```java
// ❌ 难以测试和 mock
public class UserService {
    public static User createUser(String name) {
        // ...
    }
}
```

### 3. 过度使用字符串

```java
// ❌ 容易出错
String query = "SELECT * FROM users WHERE name = '" + name + "'";

// ✅ 使用 PreparedStatement
String sql = "SELECT * FROM users WHERE name = ?";
```

### 4. 忽略异常

```java
// ❌ 反模式
try {
    // ...
} catch (Exception e) {
    // 吞掉异常
}
```

---

## Java 新特性（Java 17+）

### Text Blocks

```java
// ✅ 简洁
String json = """
{
    "name": "张三",
    "age": 30
}
""";
```

### Pattern Matching

```java
if ("hello".matches("h.*")) {
    // ...
}
```

### Records（Java 16）

```java
public record User(String name, String email) {
    public User {
        this.name = name;
        this.email = email;
    }
}
```

---

## AI 编码检查清单

- [ ] 遵循命名规范（驼峰命名）
- [ ] 遵循单一职责原则
- [ ] 使用接口和抽象类
-   - 接口定义契约
-   - 抽象类提供默认实现
- [ ] 使用依赖注入（Spring）
-   - 构造器注入
-   @Autowired 字段注入
- [ ] 使用 Spring Boot 自动配置
-   - @Configuration 类
-   - @Service, @Repository 等
- [ ] 使用 @Transactional 事务管理
-   - 方法级事务
-   @Transactional(readOnly = true)
- [ ] 使用 @Controller 定义 REST API
-   - @RestController
-   - @GetMapping, @PostMapping 等
-   - @RequestParam, @PathVariable
- - @RequestBody, @ResponseBody
- [ ] 使用 Spring Data JPA
-   - 接口继承 JpaRepository
-   - 方法名查询（findByEmail）
- - Optional 返回类型
- [ ] 正确处理异常
-   - @ControllerAdvice + @ExceptionHandler
-   - 自定义异常类
-   - 全局异常处理
- [ ] 使用日志（SLF4J）
-   - @Slf4j 注解
-   - 分级日志
- - 正确使用日志级别
- [ ] 编写单元测试
-   - JUnit 5 + Mockito
-   | 使用 @MockBean
-   | Mock 依赖
-   | when/verify
- [ ] 遵循安全编码规范
-   - 输入验证
-   - 密码加密（BCrypt）
   - SQL 注入防护
   - 使用 PreparedStatement
- [ ] 避免过度设计
-   - KISS 原则
-   - YAGNI 原则
- [ ] 考虑性能
   - 使用缓存
   - 异步处理（@Async）
   - 数据库索引
-   - 避免 N+1 查询
- [ ] 代码审查要点
-   - 代码可读性
-   - 注释清楚
   - 命名有意义
-   - 避免魔法数字
   - 避免硬编码
