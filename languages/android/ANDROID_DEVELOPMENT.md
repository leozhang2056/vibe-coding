# Android 开发规范

> Vibe Coding for Android - AI 辅助 Android 开发规范

## 项目结构

### 推荐的目录结构

```
MyApplication/
├── app/
│   ├── src/
│   │   ├── main/java/
│   │   │   ├── MainActivity.kt
│   │   │   ├── data/
│   │   │   ├── ui/
│   │   │   ├── utils/
│   │   │   └── MyApplication.kt
│   │   ├── res/
│   │   │   ├── layout/
│   │   │   ├── values/
│   │   │   ├── drawable/
│   │   │   └── mipmap/
│   │   └── AndroidManifest.xml
├── build.gradle (Module)
├── build.gradle (Project)
└── proguard-rules.pro
```

---

## Kotlin 编码规范

### 命名规范

```kotlin
// ✅ 正确
class UserManager {}
fun getUserName() {}
const val MAX_RETRY_COUNT = 3

// ❌ 错误
class userManager {}
fun getUserName() {}
const val max_retry_count = 3
```

| 类型 | 规范 | 示例 |
|------|------|------|
| 类 | PascalCase | `UserManager` |
| 函数 | camelCase | `getUserName()` |
| 变量 | camelCase | `userName` |
| 常量 | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| 包名 | 小写 | `com.example.app` |

---

## 架构模式

### MVVM + Repository 模式

```kotlin
// ViewModel
class UserViewModel(
    private val repository: UserRepository
) : ViewModel() {
    private val _users = MutableStateFlow<List<User>>(emptyList())
    val users: StateFlow<List<User>> = _users.asStateFlow()
    
    fun loadUsers() {
        viewModelScope.launch {
            val result = repository.getUsers()
            _users.value = result
        }
    }
}

// Repository
class UserRepository(
    private val api: UserApi,
    private val database: AppDatabase
) {
    suspend fun getUsers(): List<User> {
        return api.getUsers()
    }
}
```

---

## Jetpack Compose 规范

### Compose 函数原则

```kotlin
// ✅ 正确
@Composable
fun UserList(
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

// ❌ 错误（函数太长）
@Composable
fun UserList(users: List<User>, onUserClick: (User) -> Unit) {
    // ... 100+ 行代码
}
```

### 状态管理

```kotlin
// ✅ 正确（状态提升）
@Composable
fun UserScreen(viewModel: UserViewModel) {
    val users by viewModel.users.collectAsState()
    
    UserList(
        users = users,
        onUserClick = { user -> viewModel.onUserClick(user) }
    )
}
```

---

## 协程使用

### 协程最佳实践

```kotlin
// ✅ 正确
viewModelScope.launch {
    try {
        val result = api.getData()
        _result.value = Result.success(result)
    } catch (e: Exception) {
        _result.value = Result.error(e)
    }
}

// ❌ 错误（阻塞主线程）
val result = api.getData() // 在主线程执行网络请求
```

---

## 资源管理

### 字符串资源

```xml
<!-- strings.xml -->
<string name="app_name">我的应用</string>
<string name="welcome_message">欢迎</string>
```

### 使用资源

```kotlin
// ✅ 正确
textView.text = getString(R.string.welcome_message)

// ❌ 错误（硬编码）
textView.text = "欢迎"
```

---

## 生命周期管理

### 生命周期感知

```kotlin
@Composable
fun HomeScreen(
    viewModel: HomeViewModel,
    lifecycleOwner: LifecycleOwner = rememberSaveable()
) {
    val data by viewModel.data.observeAsState()
    
    LaunchedEffect(Unit, lifecycleOwner) {
        viewModel.loadData()
    }
}
```

---

## 网络请求

### Retrofit + 协程

```kotlin
// API 接口
interface UserApi {
    @GET("users")
    suspend fun getUsers(): Response<List<User>>
}

// Repository
class UserRepository(private val api: UserApi) {
    suspend fun getUsers(): List<User> {
        return api.getUsers().body() ?: emptyList()
    }
}
```

---

## 数据库

### Room 使用

```kotlin
// Entity
@Entity
data class User(
    @PrimaryKey val id: Int,
    @ColumnInfo(name = "name") val name: String,
    @ColumnInfo(name = "email") val email: String
)

// DAO
@Dao
interface UserDao {
    @Query("SELECT * FROM users")
    fun getAll(): Flow<List<User>>
    
    @Insert
    fun insert(user: User): Long
}

// Database
@Database(entities = [User::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
}
```

---

## 依赖注入

### Hilt 使用

```kotlin
// Module
@Module
@InstallIn(Singleton::class)
class DataModule {
    @Provides
    @Singleton
    fun provideUserApi(retrofit: Retrofit): UserApi {
        return retrofit.create(UserApi::class.java)
}

// ViewModel 注入
@HiltViewModel
class UserViewModel @Inject constructor(
    private val repository: UserRepository
) : ViewModel()
```

---

## 测试

### 单元测试

```kotlin
// ViewModel 测试
@Test
fun testUserViewModel_loadUsers() = runTest {
    // Given
    val repository = mockk<UserRepository>()
    val viewModel = UserViewModel(repository)
    val users = listOf(User(1, "Alice"))
    whenever(repository.getUsers()).thenReturn(users)
    
    // When
    viewModel.loadUsers()
    
    // Then
    assertEquals(users, viewModel.users.value)
}
```

### UI 测试

```kotlin
@Test
fun testUserList_showsCorrectData() {
    composeTest {
        val users = listOf(User(1, "Alice"), User(2, "Bob"))
        
        UserList(
            users = users,
            onUserClick = { /* 测试逻辑 */ }
        )
        // 验证
    }
}
```

---

## 性能优化

### Lazy 加载

```kotlin
// ✅ 正确
LazyColumn {
    items(items = userList, key = { it.id }) {
        UserItem(user = it)
    }
}

// ❌ 错误（一次加载所有）
LazyColumn {
    items(items = LoadState(Utils.getAllUsers())) {
        UserItem(user = it)
    }
}
```

### 内存优化

```kotlin
// ✅ 正确（使用 StateFlow）
@Composable
fun MyScreen(viewModel: MyViewModel) {
    val data by viewModel.data.collectAsState()
}

// ❌ 错误（在 Composable 中直接使用 LiveData）
@Composable
fun MyScreen(viewModel: MyViewModel) {
    val data by viewModel.data.observeAsState()
}
```

---

## 反模式（避免）

### 1. Global State

```kotlin
// ❌ 反模式
object GlobalState {
    var currentUser: User? = null
}

// ✅ 正确（依赖注入）
class SessionManager(
    private val userRepository: UserRepository
) {
    var currentUser: User? = null
}
```

### 2. Context Leaking

```kotlin
// ❌ 错误（Activity 泄漏）
class MyActivity : AppCompatActivity() {
    // 非静态的 Context 依赖
    private val manager = MyManager(this)
}

// ✅ 正确
class MyActivity : AppCompatActivity() {
    private val manager: MyManager by viewModels()
}
```

### 3. 回调地狱

```kotlin
// ❌ 反模式
button.setOnClickListener {
    api.call { result ->
        result.getMoreData { more ->
            ui.update(more)
        }
    }
}

// ✅ 正确（协程）
lifecycleScope.launch {
    val result = api.call()
    val more = result.getMoreData()
    ui.update(more)
}
```

---

## AI 编码检查清单

- [ ] 遵循 Kotlin 编码规范
- [ ] 使用 MVVM + Repository 模式
- [ ] 使用 Jetpack Compose（非 XML）
-   - 使用协程处理异步
-   - 状态提升
-   - 避免内存泄漏
-   - 使用 LazyColumn/Item
- [ ] Room 数据库 + Flow
- [ ] Hilt 依赖注入
- [ ] Retrofit + 协程
- [ ] 包含单元测试和 UI 测试
- [ ] 遵循 Material Design 原则
