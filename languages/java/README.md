# Java Vibe Coding

> AI 辅助 Java 开发完整指南

## 为什么选择 Java？

Java 是企业级应用的首选语言：
- 成熟稳定的生态系统
- Spring Boot 快速开发
- 强大的类型系统
- 丰富的第三方库
- 完善的监控工具

---

## 📚 核心文档

1. **[阿里巴巴 Java 开发规范](./ALIBABA_STANDARDS.md)** - 必读！阿里泰山版 13 问精华
2. **[开发规范](./DEVELOPMENT.md)** - 完整的 Java 编码规范
3. **[工具类库](./UTILITIES.md)** - 常用工具类封装
4. **[CLAUDE.md](./CLAUDE.md)** - AI 编程提示词模板

---

## 快速开始

### 技术栈

```
- 语言: Java 17+
- 框架: Spring Boot 3.x
- 构建: Maven/Gradle
- 数据库: PostgreSQL / MySQL
- ORM: Spring Data JPA
- 测试: JUnit 5 + Mockito
- 日志: SLF4J
- 缓存: Spring Cache
```

### 项目初始化

#### 使用 Spring Initializr

访问 https://start.spring.io/

选择：
- Project: Maven Project
- Language: Java 17
- Spring Boot: 3.x
- Dependencies: Spring Web, Spring Data JPA, PostgreSQL Driver, Lombok
- Generate: 

或者使用 CLI:

```bash
curl https://start.spring.io/starter.zip \
  -d type=maven-project \
  -d language=java \
  -d bootVersion=3.2.0 \
  -d dependencies=web,data-jpa,postgresql,lombok \
  -d name=demo
```

---

## 核心概念

### 1. 依赖注入（DI）

```java
// 构造器注入
@Service
public class UserService {
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}

// 字段注入
@Service
public class UserController {
    @Autowired
    private UserService userService;
}
```

### 2. AOP（面向切面编程）

```java
// 日志切面
@Aspect
@Component
public class LoggingAspect {
    @Around("execution(* com.example.*.*(..))")
    public Object logMethod(ProceedingJoinPoint pjp) throws Throwable {
        long start = System.currentTimeMillis();
        
        Object result = pjp.proceed();
        
        long duration = System.currentTimeMillis() - start;
        System.out.printf("[%.3fs] %s.%s - %d ms\n",
            duration,
            pjp.getSignature().getDeclaringType(),
            pjp.getSignature().getName()
        );
        
        return result;
    }
}
```

### 3. 事务管理

```java
@Service
@Transactional
public class OrderService {
    
    @Transactional
    public void createOrder(Order order) {
        // 所有数据库操作在一个事务中
        orderRepository.save(order);
        orderItemRepository.saveAll(order.getItems());
        paymentService.process(order);
    }
}
```

---

## 完整示例：用户 REST API

### Model

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
    Optional<User> findByEmail(String email);
    List<User> findByAgeGreaterThan(Integer age);
}
```

### Service

```java
@Service
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    public User create(String name, String email, Integer age) {
        User user = new User();
        user.setName(name);
        user.setEmail(email);
        user.setAge(age);
        
        return userRepository.save(user);
    }
}
```

### Controller

```java
@RestController
@RequestMapping("/api/users")
@Tag(name = "用户管理")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @GetMapping
    public List<User> getAll() {
        return userService.getAll();
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<User> getById(@PathVariable Long id) {
        return userService.findById(id)
            .map(user -> ResponseEntity.ok().body(user))
            .orElse(ResponseEntity.notFound().build());
    }
    
    @PostMapping
    public User create(@RequestBody UserCreateRequest request) {
        return userService.create(request);
    }
}
```

---

## Spring Boot 最佳实践

### 1. 配置外部化

```properties
# application.properties
spring.application.name=myapp
spring.profiles.active=dev

# 开发环境配置
spring.datasource.url=jdbc:postgresql://localhost:5432/mydb
spring.datasource.username=postgres
spring.datasource.password=postgres
```

### 2. Profile

```properties
# application-dev.properties
spring.jpa.show-sql=true
logging.level.com.example=DEBUG
```

### 3. Configuration 类

```java
@Configuration
public class AppConfig {
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return PasswordEncoderFactories.createDelegatingPasswordEncoder();
    }
}
```

---

## 测试策略

### 单元测试

```java
@SpringBootTest
@TestMethodOrder(MethodOrder.OrderAnnotation.class)
class UserServiceIntegrationTest {
    
    @Autowired
    private UserService userService;
    
    @Test
    void shouldCreateUser() {
        // Given
        String name = "张三";
        String email = "zhangsan@example.com";
        Integer age = 30;
        
        // When
        User user = userService.create(name, email, age);
        
        // Then
        assertThat(user.getId()).isNotNull();
        assertThat(user.getName()).isEqualTo(name);
        assertThat(user.getEmail()).isEqualTo(email);
        assertThat(user.getAge()).isEqualTo(age);
    }
}
```

---

## 常见问题

### Q1: 如何处理异常？

```java
// ✅ 正确
@Service
public class UserService {
    public User findById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));
    }
}

@ExceptionHandler(UserNotFoundException.class)
public ResponseEntity<ErrorResponse> handleUserNotFound(
    UserNotFoundException ex
) {
    return ResponseEntity
        .status(HttpStatus.NOT_FOUND)
        .body(new ErrorResponse(
            ErrorCode.USER_NOT_FOUND,
            ex.getMessage()
        ));
}
```

### Q2: 如何实现分页？

```java
// Repository
public interface UserRepository extends JpaRepository<User, Long>,
                                   PaginableAndSorting<User> {
    Page<User> findAll(Pageable pageable);
}

// Controller
@GetMapping
public Page<User> getAll(
    @RequestParam(defaultValue = "0") int page,
    @RequestParam(defaultValue = "10") int size,
    @Sort(defaultValue = "name") String sort
) {
    Pageable pageable = PageRequest.of(page, size, Sort.by(sort));
    return userService.getAll(pageable);
}
```

### Q3: 如何实现缓存？

```java
@Service
class UserService {
    
    @Cacheable(value = "users", key = "#id")
    public User findById(Long id) {
        return userRepository.findById(id).orElse(null);
    }
    
    @CacheEvict(value = "users", allEntries = true)
    public void refreshCache() {
        // 刷新缓存
    }
}
```

---

## AI 编码检查清单

- [ ] 遵循 Java 命名规范（驼峰命名）
- [ ] 使用 Spring Boot 框架
-   - @RestController 定义 REST API
-   - @Service 定义服务层
-   - @Repository 定义数据访问层
- [ ] 使用 @Transactional 事务管理
-   - 方法级事务
-   - @Transactional(readOnly = true)
- [ ] 使用 Spring Data JPA
-   - 接口继承 JpaRepository
-   - 方法名查询（findByEmail）
-   - Optional 返回类型
- [ ] 正确处理异常
    - @ControllerAdvice + @ExceptionHandler
    - 自定义异常类
    - 全局异常处理
- [ ] 使用日志（SLF4J）
    - @Slf4j 注解
    - 分级日志
    - 正确使用日志级别
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
