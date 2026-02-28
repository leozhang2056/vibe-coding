# Android Vibe Coding

> AI 辅助 Android 开发完整指南

## 📚 核心文档

1. **[Android 开发模式与最佳实践](./ANDROID_PATTERNS.md)** - 基于《Android 开发范例代码大全》提炼
2. **[开发规范](./ANDROID_DEVELOPMENT.md)** - 完整的 Android 编码规范
3. **[配置管理](./CONFIGURATION.md)** - Gradle 和构建配置
4. **[CLAUDE.md](./CLAUDE.md)** - AI 编程提示词模板
5. **[快速参考](./QUICK_REFERENCE.md)** - 常用代码片段速查

---

## 快速开始

### 1. 技术栈

```
- 语言: Kotlin
- UI: Jetpack Compose
- 架构: MVVM + Repository
- 异步: Kotlin Coroutines + Flow
- 数据库: Room
- 网络: Retrofit
- DI: Hilt
```

### 2. 项目结构

```
app/
├── src/main/java/com/example/app/
│   ├── data/
│   │   ├── model/        # 数据模型
│   │   ├── repository/   # 数据仓库
│   │   └── local/        # 本地数据源
│   ├── ui/
│   │   ├── screen/       # 页面
│   │   ├── theme/        # 主题
│   │   └── component/    # 组件
│   └── MainActivity.kt
├── build.gradle (Module)
└── build.gradle (Project)
```

### 3. 核心组件

#### Model

```kotlin
@Entity
data class User(
    @PrimaryKey
    val id: Int,
    val name: String,
    val email: String
)
```

#### ViewModel

```kotlin
@HiltViewModel
class UserViewModel @Inject constructor(
    private val repository: UserRepository
) : ViewModel() {
    private val _users = MutableStateFlow<List<User>>(emptyList())
    val users: StateFlow<List<User>> = _users.asStateFlow()
    
    fun loadUsers() {
        viewModelScope.launch {
            repository.getUsers()
        }
    }
}
```

#### Repository

```kotlin
class UserRepository @Inject constructor(
    private val api: UserApi,
    private val db: AppDatabase
) {
    fun getUsers(): Flow<List<User>> = flow {
        emit(db.userDao().getAll())
        emit(api.getUsers().body() ?: emptyList())
    }
}
```

---

## 完整示例：用户列表功能

### 1. Model

```kotlin
@Entity
data class User(
    @PrimaryKey(autoGenerate = true)
    val id: Int = 0,
    val name: String,
    val email: String
)
```

### 2. DAO

```kotlin
@Dao
interface UserDao {
    @Query("SELECT * FROM users ORDER BY name ASC")
    fun getAll(): Flow<List<User>>
    
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    fun insert(user: User): Long
}
```

### 3. Database

```kotlin
@Database(
    entities = [User::class],
    version = 1
)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
}
```

### 4. API

```kotlin
interface UserApi {
    @GET("users")
    suspend fun getUsers(): Response<List<User>>
}
```

### 5. Repository

```kotlin
class UserRepository @Inject constructor(
    private val api: UserApi,
    private val db: AppDatabase
) {
    fun getUsers(): Flow<List<User>> = flow {
        // 1. 从数据库获取
        emit(db.userDao().getAll())
        
        // 2. 从 API 获取
        try {
            val apiUsers = api.getUsers().body() ?: emptyList()
            emit(apiUsers)
            
            // 3. 保存到数据库
            db.userDao().insertAll(apiUsers)
        } catch (e: Exception) {
            // 网络错误时，继续使用数据库缓存
        }
    }
}
```

### 6. ViewModel

```kotlin
@HiltViewModel
class UserViewModel @Inject constructor(
    private val repository: UserRepository
) : ViewModel() {
    private val _users = MutableStateFlow<List<User>>(emptyList())
    val users: StateFlow<List<User>> = _users.asStateFlow()
    
    fun loadUsers() {
        viewModelScope.launch {
            repository.getUsers().collect { users ->
                _users.value = users
            }
        }
    }
}
```

### 7. Screen (Jetpack Compose)

```kotlin
@Composable
fun UserScreen(
    viewModel: UserViewModel = hiltViewModel(),
    scaffold: ScaffoldState = rememberScaffoldState()
) {
    val users by viewModel.users.collectAsState()
    val isLoading by viewModel.isLoading.collectAsState()
    
    Scaffold(
        topBar = {
            TopAppBar(title = "用户列表")
        }
    ) {
        when {
            isLoading -> {
                CircularProgressIndicator()
            }
            users.isEmpty() -> {
                EmptyContent(text = "暂无用户")
            }
            else -> {
                LazyColumn {
                    items(users) { user ->
                        UserItem(
                            user = user,
                            onClick = { /* 导航到详情 */ }
                        )
                    }
                }
            }
        }
    }
    
    LaunchedEffect(Unit) {
        viewModel.loadUsers()
    }
}
```

---

## 常见代码片段

### 启动 Activity

```kotlin
val intent = Intent(context, TargetActivity::class.java)
startActivity(intent)
```

### Toast 消息

```kotlin
Toast.makeText(context, "操作成功", Toast.LENGTH_SHORT).show()
```

### Snackbar

```kotlin
Scaffold(
    snackbarHost = scaffoldState.snackbarHostState
) {
    Scaffold(
        // ...
    ) { snackbarResult ->
        SnackbarResult(
            action = {
                SnackbarAction(
                    label = "重试",
                    onClick = { /* 重试逻辑 */ }
                )
            )
        )
    }
}
```

### 导航

```
NavHost(
    startDestination = "home"
) {
    composable(route = "home") { HomeScreen() }
    composable(route = "profile") { ProfileScreen() }
}
```

---

## 性能优化

### LazyColumn 分页

```kotlin
LazyColumn(
    items = lazyPagingItems(
        items = allItems,
        itemKey = { it.id }
    )
) {
    items(
        items = snapshot.items
    ) {
        // 渲染项目
    }
}
```

### 图片加载（Coil）

```kotlin
AsyncImage(
    model = ImageRequest.Builder("https://example.com/image.jpg")
        .crossfade(true)
        .build(),
    placeholder = painterResource(R.drawable.placeholder),
    modifier = Modifier
        .size(100.dp)
        .clip(CircleShape)
)
```

---

## 最佳实践

### 1. 避免 Context 泄漏

```kotlin
// ❌ 错误
class MyActivity : AppCompatActivity() {
    private val handler = Handler() // Activity 泄漏
}

// ✅ 正确
class MyActivity : AppCompatActivity() {
    private val lifecycleOwner = this
    val handler = Handler(Looper.getMainLooper())
    
    override fun onDestroy() {
        super.onDestroy()
        // 清理资源
    }
}
```

### 2. 使用协程

```kotlin
// ✅ 正确
lifecycleScope.launch {
    // 网络请求
}

// ❌ 错误（阻塞主线程）
val result = api.getData()
```

### 3. 状态管理

```kotlin
// ✅ 正确
val text by state.text.collectAsState()

// ❌ 错误（在 Composable 中直接使用 LiveData）
```

---

## AI 编码检查清单

- [ ] 使用 Kotlin（非 Java）
- [ ] 使用 Jetpack Compose
-   - 状态提升
-   - 避免内存泄漏
-   - 使用 LazyColumn/Item
- [ ] 使用 MVVM + Repository
- [ ] 使用 Room + Flow
- [ ] 使用 Hilt DI
- [ ] 使用 Retrofit + 协程
- [ ] 使用 Material Design 3
- [ ] 包含测试
- [ ] ProGuard/R8 配置
- [ ] 遵循 Material Design 原则
