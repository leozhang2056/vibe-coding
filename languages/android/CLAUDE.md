# Android CLAUDE.md 模板

> 项目根目录的 Claude Code 约束文件

```markdown
# 项目说明

这是一个 [简短描述]，使用 Kotlin + Jetpack Compose 开发。

## 技术栈

- 语言: Kotlin
- UI: Jetpack Compose (Material 3)
- 架构: MVVM + Repository
- 异步: Kotlin Coroutines + Flow
- 数据库: Room
    - API: Retrofit + Moshi
    - DI: Hilt
- 图片: Coil
- 测试: JUnit + Robolectric

## 项目结构

```
app/
├── src/main/java/com/example/app/
│   ├── data/
│   │   ├── model/
│   │   ├── repository/
│   │   └── local/
│   ├── ui/
│   │   ├── screen/
│   │   ├── theme/
│   │   └── component/
│   ├── MyApplication.kt
│   └── MainActivity.kt
```

## 编码规范

### 1. 语言规范

- 使用 **Kotlin**（非 Java）
- 遵循 Kotlin 编码规范
- 避免过度的 Java 风格代码

### 2. 命名规范

| 类型 | 规范 | 示例 |
|------|------|------|
| 类 | PascalCase | `UserManager` |
| 函数 | camelCase | `getUserName()` |
| 变量 | camelCase | `userName` |
| 常量 | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| 包名 | 小写 | `com.example.app` |

### 3. 文件组织

- 一个文件一个类（避免巨石文件）
- 相关类放在同一目录
- 避免深层嵌套（最多 3 层）

## 架构要求

### 1. MVVM + Repository

```kotlin
// ViewModel
class UserViewModel(
    private val repository: UserRepository
) : ViewModel() {
    private val _users = MutableStateFlow<List<User>>(emptyList())
    val users: StateFlow<List<User>> = _users.asStateFlow()
}
```

### 2. 单一职责

- ViewModel 只负责 UI 逻辑
- Repository 负责数据获取
- Model 只负责数据定义

### 3. 依赖方向

- UI → ViewModel → Repository → API/Database
- 单向依赖，双向通信通过 Flow

## UI 规范

### Jetpack Compose

```kotlin
// ✅ 正确
@Composable
fun UserScreen(
    users: List<User>,
    onUserClick: (User) -> Unit
) {
    LazyColumn {
        items(users) { user ->
            UserItem(
                user = user,
                onClick = { onUserClick(user) }
            )
        }
    }
}
```

### 状态管理

```kotlin
// ✅ 正确（状态提升）
@Composable
fun UserScreen(viewModel: UserViewModel) {
    val users by viewModel.users.collectAsState()
    // ...
}
```

## 网络请求

### Retrofit + 协程

```kotlin
// ✅ 正确
class UserRepository(
    private val api: UserApi,
    private val db: AppDatabase
) {
    suspend fun getUsers(): Result<List<User>> = try {
        Result.success(api.getUsers().body() ?: emptyList())
    } catch (e: Exception) {
        Result.failure(e)
    }
}
```

## 数据库

### Room + Flow

```kotlin
@Dao
interface UserDao {
    @Query("SELECT * FROM users")
    fun getAll(): Flow<List<User>>
}

class UserRepository(
    private val api: UserApi,
    private val db: AppDatabase
) {
    fun getUsers(): Flow<List<User>> = flow {
        emit(db.userDao().getAll())
        emit(api.getUsers().body() ?: emptyList())
    }
}
```

## 安全要求

### 1. 网络安全

- 使用 HTTPS
- 证书固定（SSL Pinning）
- 证书透明度

### 2. 数据安全

- Room 数据库加密
- 敏感数据使用 EncryptedSharedPreferences
- 密码不记录日志

### 3. 代码安全

- ProGuard/R8 混淆
- 不在代码中硬编码密钥
- 敏感信息使用 AndroidKeyStore

## 性能要求

### 1. 内存优化

- 使用 LazyColumn/Item（懒加载）
- 及时释放资源
- 避免 Context 泄漏

### 2. 网络优化

- 使用 OkHttp 连接池
- 缓存策略
- 图片压缩

### 3. 启动优化

- 使用 Startup 提供者
- 懒后初始化非关键组件
- 延迟加载

## 测试要求

### 1. 单元测试

- 每个 ViewModel 都有测试
- 使用 MockK
- 覆盖关键逻辑

### 2 UI 测试

- 使用 Compose Testing
- 测试关键用户流程
- 截图测试

### 3. 集成测试

- 测试完整流程
- 使用 Hilt 测试环境
- Mock API 响应

## 禁止事项

```kotlin
// ❌ 不要这样做
// 1. 主线程网络请求
val result = api.getData() // 错误！

// 2. View 泄漏
class MyActivity : AppCompatActivity() {
    private val handler = Handler() // 错误！
}

// 3. 全局状态
object GlobalState { // 错误！
    var currentUser: User? = null
}

// 4. 回调地狱
api.call { result ->
    result.getMore { data ->  // 嵌套回调！
        updateUI(data)
    }
}
```

## 工作流程

1. 创建功能分支
2. 实现 Model + ViewModel + Repository
3. 实现 UI（Jetpack Compose）
4. 添加单元测试
5. 添加 UI 测试
6. 代码审查
7. 合并主分支

## 提交规范

### Commit Message

```
feat(auth): add JWT token storage

fix(ui): resolve layout overlap

refactor(user): simplify user model

test(auth): add login unit test

docs(readme): update setup instructions
```

## 常见任务

### 添加新功能

```
添加用户资料编辑功能，包含：
1. 头像上传
2. 昵称修改
3. 密码修改

遵循 MVVM 架构，使用 Jetpack Compose 实现。
```

### 修复 Bug

```
修复登录后不刷新用户信息的问题。
问题：SharedFlow 未正确收集数据。
位置：UserViewModel
```

---

## Claude Code 特定功能

### 在 Android 项目中使用 Claude Code

#### 1. 修改布局

```
修改 activity_main.xml，添加一个新的按钮，
放在现有按钮下方，点击后显示 Toast 消息。
```

#### 2. 跨文件修改

```
在 User 类中添加 email 字段，
并更新 UserRepository 中的所有相关方法。
```

#### 3. 运行测试

```
运行所有单元测试，如果有失败的，
分析日志并修复。
```

---

## AI 编码检查清单

编码时，确保：

- [ ] 使用 Kotlin
- [ ] 使用 Jetpack Compose
-   - 使用协程处理异步
-   - 状态提升
-   - 避免内存泄漏
-   - 使用 LazyColumn/Item
- [ ] 使用 MVVM + Repository
- [ ] 使用 Room 数据库
-   - Flow 返回数据
- [ ] 使用 Hilt 依赖注入
- [ ] 使用 Material Design 3
- [ ] 遵循 Material Design 原则
- [ ] 包含测试
- [ ] ProGuard 配置正确
