# Alibaba Java Development Standards (Taishan Edition)

> 阿里巴巴 Java 开发手册（泰山版）核心规约总结

## 概述

阿里巴巴 Java 开发手册是业界广泛认可的 Java 编码规范，泰山版是其最新版本。本文档提炼了其中的核心规约，特别关注了容易导致 Bug 的 13 个关键问题。

---

## 一、命名规约

### 1.1 类命名

```java
// ✅ 正确 - 使用驼峰命名，首字母大写
public class UserService {}
public class OrderController {}
public class UserRepository {}

// ❌ 错误
public class user_service {}
public class ordercontroller {}
```

### 1.2 方法命名

```java
// ✅ 正确 - 使用驼峰命名，首字母小写
public void getUserById() {}
public boolean isValid() {}
public int calculateTotal() {}

// ❌ 错误
public void GetUserById() {}
public boolean IsValid() {}
```

### 1.3 常量命名

```java
// ✅ 正确 - 全部大写，下划线分隔
public static final int MAX_SIZE = 100;
public static final String DEFAULT_CHARSET = "UTF-8";

// ❌ 错误
public static final int maxSize = 100;
public static final String defaultCharset = "UTF-8";
```

### 1.4 变量命名规范

```java
// ❌ 禁止使用 isSuccess 作为变量名
// 原因：部分框架会自动解析 is 开头的变量为布尔类型
Boolean isSuccess = true;  // 危险！

// ✅ 正确的命名
Boolean success = true;
Boolean valid = true;
Boolean hasPermission = true;
```

---

## 二、三目运算符的空指针问题（核心问题 1）

### 2.1 问题重现

```java
// ❌ 危险代码 - 会抛出 NullPointerException
boolean flag = true;
Boolean nullBoolean = null;
boolean simpleBoolean = false;

// 当 nullBoolean 为 null 时，这里会抛 NPE
boolean result = flag ? nullBoolean : simpleBoolean;
```

### 2.2 原因分析

**根本原因**：三目运算符在编译时会进行**类型对齐**。

根据 Java 语言规范（JLS 15.25）：
- 当第二位和第三位操作数一个是基本类型，一个是包装类型时
- 编译器会自动对包装类型进行**拆箱**操作
- 如果包装类型为 null，就会抛出 NullPointerException

### 2.3 正确做法

```java
// ✅ 方案 1: 保持类型一致（推荐）
Boolean result = flag ? nullBoolean : simpleBoolean;

// ✅ 方案 2: 使用 null 安全处理
boolean result = flag ? 
    (nullBoolean != null ? nullBoolean : false) : 
    simpleBoolean;

// ✅ 方案 3: 使用 Optional
boolean result = flag ? 
    Optional.ofNullable(nullBoolean).orElse(false) : 
    simpleBoolean;
```

### 2.4 反编译验证

```java
// 原代码
boolean result = flag ? nullBoolean : simpleBoolean;

// 反编译后（编译器自动拆箱）
boolean result = flag ? nullBoolean.booleanValue() : simpleBoolean;
// 当 nullBoolean 为 null 时，nullBoolean.booleanValue() 抛 NPE
```

---

## 三、集合使用规范

### 3.1 HashMap 初始化容量

**为什么要设置初始化容量？**

HashMap 默认容量是 16，负载因子是 0.75。当元素数量超过容量的 75% 时，会进行扩容（rehash），扩容过程会：

1. 创建新的数组（容量翻倍）
2. 重新计算所有元素的哈希值
3. 将所有元素重新分配到新数组

这个过程非常消耗性能！

**正确做法**：

```java
// ✅ 计算预期容量并设置
// 公式：expectedSize / 0.75 + 1
int expectedSize = 1000;
Map<String, User> userMap = new HashMap<>((int)(expectedSize / 0.75) + 1);

// ✅ 使用 Guava 的 Maps.newHashMapWithExpectedSize
Map<String, User> userMap = Maps.newHashMapWithExpectedSize(1000);

// ❌ 错误 - 不设置容量
Map<String, User> userMap = new HashMap<>();
```

**扩容次数对比**：

```
不设置容量，插入 1000 个元素：扩容 7 次
设置容量 1000：扩容 0 次
```

### 3.2 ArrayList 的 subList 方法

**问题**：subList 返回的是原列表的**视图**，不是新列表！

```java
// ❌ 危险操作
List<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
List<Integer> subList = list.subList(1, 3);

// 修改原列表
list.add(6);

// 抛出 ConcurrentModificationException
subList.get(0);
```

**正确做法**：

```java
// ✅ 创建新列表
List<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
List<Integer> subList = new ArrayList<>(list.subList(1, 3));

// 安全 - 互不影响
list.add(6);
subList.get(0);  // 正常执行
```

**注意事项**：

1. subList 返回的视图和原列表共享数据
2. 修改任何一个列表会影响另一个
3. 修改原列表的结构（增删元素）会导致 subList 失效
4. 推荐使用 `new ArrayList<>(list.subList(...))` 创建独立副本

### 3.3 禁止在 foreach 循环中 remove/add

```java
// ❌ 危险 - 会抛出 ConcurrentModificationException
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));
for (String item : list) {
    if ("B".equals(item)) {
        list.remove(item);  // ❌ 错误！
    }
}

// ✅ 方案 1: 使用 Iterator
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String item = iterator.next();
    if ("B".equals(item)) {
        iterator.remove();  // ✅ 正确
    }
}

// ✅ 方案 2: 使用 removeIf（推荐）
list.removeIf("B"::equals);

// ✅ 方案 3: 使用 Stream
list = list.stream()
    .filter(item -> !"B".equals(item))
    .collect(Collectors.toList());
```

**原因**：foreach 循环使用了 Iterator，使用 List 的 remove 方法会修改 modCount，导致 Iterator 检测到并发修改异常。

---

## 四、线程池规范（核心问题 2）

### 4.1 禁止使用 Executors 创建线程池

**为什么禁止？**

Executors 提供的工厂方法存在严重问题：

```java
// ❌ 错误 - FixedThreadPool
ExecutorService fixedThreadPool = Executors.newFixedThreadPool(10);
// 问题：使用无界队列（LinkedBlockingQueue）
// 后果：任务堆积可能导致 OOM

// ❌ 错误 - CachedThreadPool
ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
// 问题：线程数无限制（Integer.MAX_VALUE）
// 后果：创建过多线程导致 OOM

// ❌ 错误 - SingleThreadPool
ExecutorService singleThreadPool = Executors.newSingleThreadExecutor();
// 问题：使用无界队列
// 后果：任务堆积可能导致 OOM
```

### 4.2 正确做法 - 使用 ThreadPoolExecutor

```java
// ✅ 正确 - 使用 ThreadPoolExecutor
ThreadPoolExecutor executor = new ThreadPoolExecutor(
    5,                          // corePoolSize: 核心线程数
    10,                         // maximumPoolSize: 最大线程数
    60L,                        // keepAliveTime: 空闲线程存活时间
    TimeUnit.SECONDS,           // 时间单位
    new ArrayBlockingQueue<>(100),  // 有界队列
    new ThreadFactoryBuilder().setNameFormat("worker-%d").build(),  // 线程工厂
    new ThreadPoolExecutor.CallerRunsPolicy()  // 拒绝策略
);
```

### 4.3 线程池参数说明

| 参数 | 说明 | 推荐值 |
|------|------|--------|
| corePoolSize | 核心线程数 | CPU 密集型：CPU 核心数 + 1<br>IO 密集型：CPU 核心数 × 2 |
| maximumPoolSize | 最大线程数 | 根据任务执行时间和系统资源调整 |
| keepAliveTime | 空闲线程存活时间 | 60 秒 |
| workQueue | 工作队列 | 推荐有界队列，避免 OOM |
| handler | 拒绝策略 | 根据业务需求选择 |

### 4.4 拒绝策略

```java
// 1. AbortPolicy（默认）：抛出异常
new ThreadPoolExecutor.AbortPolicy()

// 2. CallerRunsPolicy：调用者线程执行
new ThreadPoolExecutor.CallerRunsPolicy()

// 3. DiscardPolicy：直接丢弃
new ThreadPoolExecutor.DiscardPolicy()

// 4. DiscardOldestPolicy：丢弃最老的任务
new ThreadPoolExecutor.DiscardOldestPolicy()

// ✅ 推荐：自定义拒绝策略
new RejectedExecutionHandler() {
    @Override
    public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
        // 记录日志
        log.warn("Task rejected, active: {}, queue: {}", 
            e.getActiveCount(), e.getQueue().size());
        // 执行降级逻辑
        fallbackHandler.handle(r);
    }
}
```

---

## 五、字符串拼接规范

### 5.1 禁止在 for 循环中使用 "+" 拼接

```java
// ❌ 错误 - 每次循环都创建新的 String 对象
String result = "";
for (int i = 0; i < 1000; i++) {
    result += i;  // 性能极差！
}

// ✅ 方案 1: 使用 StringBuilder
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i);
}
String result = sb.toString();

// ✅ 方案 2: 使用 String.join（Java 8+）
List<String> items = new ArrayList<>();
for (int i = 0; i < 1000; i++) {
    items.add(String.valueOf(i));
}
String result = String.join(",", items);

// ✅ 方案 3: 使用 Stream
String result = IntStream.range(0, 1000)
    .mapToObj(String::valueOf)
    .collect(Collectors.joining(","));
```

### 5.2 性能对比

```
方式                    | 1000次拼接耗时 | 10000次拼接耗时
-----------------------|----------------|-----------------
"+" 拼接                | 150ms          | 8000ms
StringBuilder           | 1ms            | 5ms
String.join             | 2ms            | 15ms
Stream.collect          | 3ms            | 20ms
```

**结论**：使用 StringBuilder 性能最优！

---

## 六、日期时间规范

### 6.1 禁止使用 SimpleDateFormat

```java
// ❌ 错误 - SimpleDateFormat 不是线程安全的
private static final SimpleDateFormat DATE_FORMAT = 
    new SimpleDateFormat("yyyy-MM-dd");

public void format(Date date) {
    // 多线程环境下会抛出异常或产生错误结果
    return DATE_FORMAT.format(date);
}

// ✅ 方案 1: 使用 DateTimeFormatter（Java 8+，线程安全）
private static final DateTimeFormatter DATE_FORMATTER = 
    DateTimeFormatter.ofPattern("yyyy-MM-dd");

public String format(LocalDate date) {
    return DATE_FORMATTER.format(date);
}

// ✅ 方案 2: 使用 ThreadLocal
private static final ThreadLocal<SimpleDateFormat> DATE_FORMAT = 
    ThreadLocal.withInitial(() -> new SimpleDateFormat("yyyy-MM-dd"));

// ✅ 方案 3: 每次创建新实例（低性能但安全）
public String format(Date date) {
    return new SimpleDateFormat("yyyy-MM-dd").format(date);
}
```

### 6.2 推荐使用 Java 8+ 时间 API

```java
// ✅ 使用 java.time 包（线程安全，API 清晰）
LocalDate today = LocalDate.now();
LocalDateTime now = LocalDateTime.now();
ZonedDateTime utc = ZonedDateTime.now(ZoneId.of("UTC"));

// 格式化
String formatted = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")
    .format(now);

// 解析
LocalDateTime parsed = LocalDateTime.parse(
    "2024-01-01 12:00:00",
    DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")
);
```

---

## 七、序列化规范

### 7.1 禁止修改 serialVersionUID

```java
// ❌ 错误 - 修改 serialVersionUID 会导致反序列化失败
public class User implements Serializable {
    private static final long serialVersionUID = 1L;  // V1
    // ... 字段定义
}

// V2 版本
public class User implements Serializable {
    private static final long serialVersionUID = 2L;  // ❌ 修改了！
    private String email;  // 新增字段
    // ... 其他字段
}

// V1 序列化的数据无法用 V2 反序列化！
```

**正确做法**：

```java
// ✅ 一旦确定，不要修改 serialVersionUID
public class User implements Serializable {
    // 使用 IDE 生成或使用固定的值
    private static final long serialVersionUID = 123456789L;
    
    // 如果需要版本兼容，使用 readResolve/writeReplace
    private Object readResolve() throws ObjectStreamException {
        // 版本兼容逻辑
        return this;
    }
}
```

### 7.2 序列化最佳实践

```java
// ✅ 使用默认的 serialVersionUID（由 IDE 生成）
private static final long serialVersionUID = 1L;

// ✅ 序列化时忽略敏感字段
public class User implements Serializable {
    private static final long serialVersionUID = 1L;
    
    private String username;
    private transient String password;  // 不序列化密码
    private transient Logger logger;   // 不序列化日志对象
}

// ✅ 使用 Externalizable 自定义序列化
public class User implements Externalizable {
    @Override
    public void writeExternal(ObjectOutput out) throws IOException {
        out.writeObject(username);
        // 自定义序列化逻辑
    }
    
    @Override
    public void readExternal(ObjectInput in) throws IOException, ClassNotFoundException {
        username = (String) in.readObject();
        // 自定义反序列化逻辑
    }
}
```

---

## 八、数据库规范

### 8.1 禁止使用 count(列名) 或 count(常量)

```java
// ❌ 错误
@Query("SELECT COUNT(u.id) FROM User u")
long countById();

@Query("SELECT COUNT(1) FROM User u")
long countByConstant();

// ✅ 正确 - 使用 COUNT(*)
@Query("SELECT COUNT(*) FROM User u")
long countAll();
```

**原因**：

1. `COUNT(*)` 会统计所有行（包括 NULL）
2. `COUNT(列名)` 会忽略 NULL 值
3. `COUNT(*)` 在大多数数据库中性能最优
4. `COUNT(*)` 语义更清晰

### 8.2 分页查询规范

```java
// ✅ 正确的分页查询
@Query("SELECT u FROM User u WHERE u.status = :status")
Page<User> findByStatus(@Param("status") String status, Pageable pageable);

// 使用
Pageable pageable = PageRequest.of(0, 20, Sort.by("createdAt").descending());
Page<User> users = userRepository.findByStatus("ACTIVE", pageable);
```

---

## 九、日志规范

### 9.1 禁止直接使用日志框架 API

```java
// ❌ 错误 - 直接使用 Log4j 或 Logback API
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class UserService {
    private static final Logger logger = LogManager.getLogger(UserService.class);
    
    public void createUser() {
        logger.info("Creating user...");  // 绑定了具体实现
    }
}

// ✅ 正确 - 使用 SLF4J（门面模式）
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class UserService {
    private static final Logger log = LoggerFactory.getLogger(UserService.class);
    
    public void createUser() {
        log.info("Creating user...");  // 使用门面接口
    }
}
```

### 9.2 日志占位符

```java
// ✅ 正确 - 使用占位符
log.info("User created: id={}, name={}", user.getId(), user.getName());

// ❌ 错误 - 字符串拼接
log.info("User created: id=" + user.getId() + ", name=" + user.getName());
```

### 9.3 日志级别使用

```java
// ERROR: 系统错误，需要立即处理
log.error("Failed to create user", exception);

// WARN: 警告信息，不影响系统运行
log.warn("User already exists: {}", username);

// INFO: 关键业务流程
log.info("Order created: id={}, amount={}", orderId, amount);

// DEBUG: 调试信息，生产环境关闭
log.debug("User details: {}", user);

// TRACE: 更详细的调试信息
log.trace("Method entry: className={}, methodName={}", className, methodName);
```

---

## 十、异常处理规范

### 10.1 捕获具体异常

```java
// ❌ 错误 - 捕获过于宽泛
try {
    // 业务代码
} catch (Exception e) {
    log.error("Error", e);
}

// ✅ 正确 - 捕获具体异常
try {
    // 业务代码
} catch (NullPointerException e) {
    log.error("NPE occurred", e);
} catch (IllegalArgumentException e) {
    log.error("Invalid argument", e);
} catch (Exception e) {
    log.error("Unexpected error", e);
}
```

### 10.2 不要吞掉异常

```java
// ❌ 错误 - 吞掉异常
try {
    riskyOperation();
} catch (Exception e) {
    // 什么都不做
}

// ✅ 正确 - 至少记录日志
try {
    riskyOperation();
} catch (Exception e) {
    log.error("Operation failed", e);
    // 或者抛出给上层处理
    throw new BusinessException("Operation failed", e);
}
```

---

## 十一、并发编程规范

### 11.1 谨慎使用继承

```java
// ❌ 错误 - 不恰当的继承
public class MyList extends ArrayList<String> {
    @Override
    public String remove(int index) {
        // 修改了父类行为
        return super.remove(index);
    }
}

// ✅ 正确 - 使用组合
public class MyList {
    private final List<String> delegate = new ArrayList<>();
    
    public String remove(int index) {
        // 自定义实现
        return delegate.remove(index);
    }
}
```

### 11.2 线程安全

```java
// ✅ 使用线程安全的集合
List<String> list = Collections.synchronizedList(new ArrayList<>());
Map<String, String> map = new ConcurrentHashMap<>();

// ✅ 使用不可变对象
public final class ImmutableUser {
    private final String name;
    private final int age;
    
    public ImmutableUser(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Getter only, no Setter
}
```

---

## 十二、NPE 防护最佳实践

### 12.1 Optional 使用

```java
// ✅ 使用 Optional 避免 NPE
public Optional<User> findById(Long id) {
    return userRepository.findById(id);
}

// 调用方
Optional<User> userOpt = userService.findById(1L);
if (userOpt.isPresent()) {
    User user = userOpt.get();
    // 处理用户
}

// 使用 map/flatMap
String username = userOpt
    .map(User::getName)
    .orElse("Anonymous");

// 使用 orElseThrow
User user = userOpt.orElseThrow(() -> new UserNotFoundException(id));
```

### 12.2 Objects 工具类

```java
// ✅ 使用 Objects.requireNonNull 进行参数校验
public void setUser(User user) {
    this.user = Objects.requireNonNull(user, "user cannot be null");
}

// ✅ 使用 Objects.equals 避免 NPE
if (Objects.equals(str1, str2)) {
    // 相等逻辑
}

// ✅ 使用 Objects.hashCode
@Override
public int hashCode() {
    return Objects.hash(name, age, email);
}
```

---

## 十三、检查清单

### 编码前检查

- [ ] 类名使用驼峰命名，首字母大写
- [ ] 方法名使用驼峰命名，首字母小写
- [ ] 常量全大写，下划线分隔
- [ ] 避免使用 isSuccess 作为变量名
- [ ] 三目运算符保持类型一致

### 集合使用

- [ ] HashMap 设置合适的初始容量
- [ ] ArrayList.subList 创建新列表
- [ ] foreach 使用 Iterator.remove() 或 removeIf()

### 线程池

- [ ] 使用 ThreadPoolExecutor 而不是 Executors
- [ ] 设置合理的核心线程数和最大线程数
- [ ] 使用有界队列
- [ ] 选择合适的拒绝策略

### 字符串

- [ ] 循环中不使用 "+" 拼接
- [ ] 使用 StringBuilder 或 Stream

### 日期时间

- [ ] 不使用 static SimpleDateFormat
- [ ] 优先使用 java.time 包

### 序列化

- [ ] serialVersionUID 保持不变
- [ ] transient 修饰敏感字段

### 数据库

- [ ] 使用 COUNT(*) 而不是 COUNT(列名)

### 日志

- [ ] 使用 SLF4J 门面
- [ ] 使用占位符而不是字符串拼接
- [ ] 正确使用日志级别

---

## 总结

阿里巴巴 Java 开发手册的核心思想：

1. **明确性**：代码意图清晰，易于理解
2. **健壮性**：避免常见陷阱和 Bug
3. **性能**：关注性能热点，优化关键路径
4. **可维护性**：统一风格，便于团队协作

遵循这些规范，可以显著提高代码质量和系统稳定性。
