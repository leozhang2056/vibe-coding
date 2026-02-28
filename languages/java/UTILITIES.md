# Java 工具类最佳实践

> 基于 AutoTask 项目 utils.py 的最佳实践

## 日志封装

```java
public final class AppLogger {
    private static final Logger logger = LoggerFactory.getLogger(AppLogger.class);
    
    private AppLogger() {}
    
    public static void info(String message) {
        logger.info(message);
    }
    
    public static void debug(String message) {
        logger.debug(message);
    }
    
    public static void warning(String message) {
        logger.warn(message);
    }
    
    public static void error(String message, Throwable e) {
        logger.error(message, e);
    }
}
```

---

## 配置加载

### application.properties

```properties
# 服务器配置
server.port=8080

# 数据源配置
spring.datasource.url=jdbc:postgresql://localhost:5432/mydb
spring.datasource.username=postgres
spring.datasource.password=postgres

# JPA
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
```

### 配置类

```java
@Configuration
@ConfigurationProperties(prefix = "app")
public class AppConfig {
    private String appVersion;
    private String featureToggle;
    private Integer maxUploadSize;
    
    // Getters
    public String getAppVersion() {
        return appVersion;
    }
}
```

---

## 常用工具类

### 字符串工具

```java
public final class StringUtils {
    
    public static boolean isBlank(String str) {
        return str == null || str.trim().isEmpty();
    }
    
    public static boolean isNotBlank(String str) {
        return !StringUtils.isBlank(str);
    }
    
    public static String join(String... strings) {
        return String.join(",", strings);
    }
}
```

### 日期时间工具

```java
public final class DateUtils {
    
    public static String format(LocalDateTime dateTime) {
        return dateTime.format(DateTimeFormatter.ISO_LOCAL_DATE_TIME);
    }
    
    public static LocalDateTime parse(String dateString) {
        return LocalDateTime.parse(dateString,
            DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")
        );
    }
}
```

### JSON 工具

```java
public final class JsonUtils {
    private static final ObjectMapper objectMapper = new ObjectMapper()
        .registerModule(new JavaTimeModule())
        .disable(SerializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);
    
    public static String toJson(Object obj) {
        try {
            return objectMapper.writeValueAsString(obj);
        } catch (JsonProcessingException e) {
            throw new RuntimeException("JSON 序列化失败", e);
        }
    }
    
    public static <T> T fromJson(String json, Class<T> clazz) {
        try {
            return objectMapper.readValue(json, clazz);
        } catch (JsonProcessingException e) {
            throw new RuntimeException("JSON 反序列化失败", e);
        }
    }
}
```

---

## 文件工具

### 文件读取

```java
public final class FileUtils {
    
    public static String readFile(String path) throws IOException {
        return new String(
            Files.readAll(Paths.get(path)),
            StandardCharsets.UTF_8
        );
    }
    
    public static void writeFile(String path, String content) throws IOException {
        Files.write(
            Paths.get(path),
            content.getBytes(StandardCharsets.UTF_8)
        );
    }
}
```

---

## 验证工具

### Bean Validation

```java
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Constraint(
    size = 2, max = 50
)
@NotBlank
public class UsernameValidation {
    @Pattern(regexp = "^[a-zA-Z0-9_]{2,50}$")
    private String username;
}
```

---

## AI 编码检查清单

- [ ] 使用 SLF4J 进行日志
-   - @Slf4j 注解
-   - 分级日志
-   - 正确使用日志级别
-   - DEBUG、INFO、WARN、ERROR
- [ ] 使用工具类封装通用逻辑
-   - 字符串工具类
-   - 日期时间工具类
-   - JSON 序列化工具
-   - 文件读取工具
- [ ] 使用 @ConfigurationProperties 外部化配置
- [ ] 使用 @Valid 进行 Bean 验证
-   - @NotBlank
   - @Size
   - @Pattern
   - @Email
- [ ] 使用 @Component 封装工具类
- [ ] 使用 @Transactional 管理事务
-   - 方法级事务
-   - @Transactional(readOnly = true)
- [ ] 使用 @Cacheable 进行缓存
-   - 指定缓存键
-   - 使用 @CacheEvict 清除缓存
- [ ] 使用 @Retryable 重试机制
-   - 指定重试次数
-   - 指定重试延迟
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
