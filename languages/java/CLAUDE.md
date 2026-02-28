# Java CLAUDE.md 模板

> Java + Spring Boot 项目根目录的 Claude Code 约束文件

```markdown
# 项目说明

这是一个 [简短描述]，使用 Java + Spring Boot 开发的 RESTful API 服务。

## 技术栈

- 语言: Java 17+
- 框架: Spring Boot 3.x
- 构建工具: Maven/Gradle
- 数据库: PostgreSQL / MySQL
- ORM: Spring Data JPA
- 测试: JUnit 5 + Mockito
- 日志: SLF4J

## 项目结构

```
src/main/java/com/example/project/
├── controller/       # 控制器层
├── service/         # 业务逻辑层
├── repository/      # 数据访问层
├── model/          # 数据模型
├── exception/      # 自定义异常
├── config/         # 配置类
├── util/           # 工具类
└── Application.java
```

## 编码规范

### 1. 命名规范

- **类**: PascalCase（如 `UserService`）
- **方法**: camelCase（如 `getUserName`）
- **常量**: UPPER_SNAKE_CASE（如 `MAX_RETRY_COUNT`）
- **包名**: 小写点分隔（如 `com.example.app`）

### 2. 文件组织

- 一个文件一个类
- 相关类放在同一包
- 避免深层嵌套（最多 3 层）

### 3. 注释规范

```java
/**
 * 用户服务类
 *
 * @author 张三
 * @since 1.0.0
 */
public class UserService {
    // ...
}
```

## 架构模式

### 三层架构

```
Controller (控制层)
    ↓
Service (业务逻辑层)
    ↓
Repository (数据访问层)
```

### 依赖注入

```java
// ✅ 正确（构造器注入）
@Service
public class UserService {
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

## Spring Boot 规范

### Controller

```java
@RestController
@RequestMapping("/api/users")
@Tag(name = "用户管理")
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
```

### Service

```java
@Service
@Transactional
public class UserService {
    
    @Cacheable("users")
    public User getUserById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));
    }
}
```

### Repository

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
}
```

## 数据库规范

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
}
```

### 查询方法

```java
@EntityGraph(attributePaths = {"user", "posts"})
@Entity
class User {
    // ...
    
    @Query("SELECT u FROM User u WHERE u.email = :email")
    Optional<User> findByEmail(@Param("email") String email);
}
```

## 异常处理

### 自定义异常

```java
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(Long userId) {
        super("User not found: " + userId);
    }
}
```

### 全局异常处理

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
}
```

## 安全要求

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
    
    public void createUser(User user, String rawPassword) {
        // 加密密码
        String encoded = passwordEncoder.encode(rawPassword);
        user.setPassword(encoded);
        
        userRepository.save(user);
    }
}
```

### SQL 注入防护

```java
// ✅ 正确
@Query("SELECT u FROM User u WHERE u.email = :email")
Optional<User> findByEmail(@Param("email") String email);

// ❌ 错误（SQL 注入风险）
String query = "SELECT * FROM users WHERE email = '" + email + "'";
```

## 测试要求

### 单元测试

```java
@SpringBootTest
class UserServiceTest {
    
    @Autowired
    private UserService userService;
    
    @MockBean
    private UserRepository userRepository;
    
    @Test
    void testGetUserById() {
        // Given
        User user = new User("张三", "zhangsan@example.com");
        when(userRepository.findById(1L))
            .thenReturn(Optional.of(user));
        
        // When
        User result = userService.findById(1L);
        
        // Then
        assertThat(result).isPresent();
        assertThat(result.get().getName()).isEqualTo("张三");
    }
}
```

### 集成测试

```java
@SpringBootTest
@AutoConfigureMockMvc
class UserControllerTest {
    
    @Autowired
    private MockMvc mockMvc;
    
    @Autowired
    private UserService userService;
    
    @Test
    void testGetUserById() throws Exception {
        mockMvc.perform(get("/api/users/1")
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name").value("张三"));
    }
}
```

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
@Configuration
@EnableCaching
@EnableScheduling
class AppConfig {
    // 配置类
}
```

## 禁止事项

```java
// ❌ 不要
1. 主线程阻塞
public List<User> getAllUsers() {
    List<User> users = new ArrayList<>();
    for (int i = 0; i < 1000; i++) {
        users.add(repository.findById((long) i).orElse(null));
    }
    return users;
}

// ✅ 推荐
public List<User> getAllUsers() {
    return userRepository.findAll();
}

// ❌ 不要
2. 过度使用 static
public class UserService {
    private static UserRepository userRepository;
}

// ✅ 推荐
public class UserService {
    private final UserRepository userRepository;
}
```

---

## 工作流程

1. 创建功能分支
2. 编写测试（TDD）
3. 实现代码
4. 运行测试确保通过
5. 代码审查
6. 合并主分支

---

## 提交规范

```
feat(user): 添加用户注册功能

fix(auth): 修复登录 bug

refactor(user): 重构用户服务

test(auth): 添加测试

docs(readme): 更新文档
```

---

## 常见任务

### 添加新功能

```
添加用户资料编辑功能，要求：
1. 修改昵称
2. 修改头像
3. 修改密码

遵循以下原则：
1. Service 层处理业务逻辑
2. Controller 层处理 HTTP
3 使用 @Transactional 确保事务性
4. 添加单元测试
```

### 修复 Bug

```
修复登录时 500 错误。
问题：用户不存在，但返回 500 而非 404。
位置：UserController
```

---

## AI 编码检查清单

编码时，确保：

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
