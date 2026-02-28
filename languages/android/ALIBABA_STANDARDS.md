# Android 阿里规范（参考阿里客户端）

> 企业级 Android 开发标准

## 技术栈

```
UI：Jetpack Compose + Material Design 3
架构：MVVM + Repository
网络：Retrofit + OkHttp3
图片：Coil
数据库：Room + DataStore
异步：Kotlin Coroutines + Flow
依赖注入：Hilt
```

---

## 项目结构

```
app/
├── src/main/java/com/company/app/
│   ├── data/
│   │   ├── model/
│   │   ├── repository/
│   │   └── local/
│   ├── ui/
│   │   ├── screen/
│   │   ├── theme/
│   │   └── component/
│   ├── MainActivity.kt
│   └── MyApplication.kt
```

---

## 命名规范

### Kotlin 特性

```kotlin
// ✅ 推荐
data class User(
    val id: Long,
    val name: String,
    val email: String
)

data class UserState(
    val isLoading: Boolean = false,
    val error: String? = null
)
```

---

## UI 规范

### Jetpack Compose

```kotlin
@Composable
fun UserScreen(
    viewModel: UserViewModel = hiltViewModel(),
    scaffoldState: ScaffoldState = rememberScaffoldState()
) {
    val uiState by viewModel.uiState.collectAsState()
    
    Scaffold(
        topBar = {
            TopAppBar(title = "用户列表")
        }
    ) {
        if (uiState.isLoading) {
            CircularProgressIndicator()
        } else {
            LazyColumn {
                items(uiState.users) { user ->
                    UserItem(
                        user = user,
                        onClick = { viewModel.onUserClick(user) }
                    )
                }
            }
        }
    }
}
```

---

## 网络规范

### Retrofit + OkHttp3

```kotlin
interface ApiService {
    @GET("users")
    suspend fun getUsers(): Response<List<User>>
}

class UserRepository @Inject constructor(
    private val api: ApiService
) {
    suspend fun getUsers(): Result<List<User>> = try {
        val response = api.getUsers()
        if (response.isSuccessful && response.body() != null) {
            Result.success(response.body()!!)
        } else {
            Result.failure(Exception("获取失败"))
        }
    } catch (e: Exception) {
        Result.failure(e)
    }
}
```

---

## 数据库规范

### Entity

```kotlin
@Entity
@Table(name = "users")
data class User(
    @PrimaryKey(autoGenerate = true)
    val id: Long = 0,
    val name: String = "",
    val email: String = "",
    @ColumnInfo(name = "created_at")
    val createdAt: LocalDateTime = LocalDateTime.now(),
    @ColumnInfo(name = "updated_at")
    var updatedAt: LocalDateTime = LocalDateTime.now()
)
```

---

## 依赖注入

### Hilt 模块

```kotlin
@Module
@InstallIn(Singleton::class)
class DataModule {
    @Provides
    @Singleton
    fun provideApiService(): ApiService {
        return Retrofit.Builder()
            .baseUrl("https://api.example.com/")
            .addConverterFactory(MoshiConverterFactory.create())
            .build()
            .create()
    }
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
        UserItem(
            user = it,
            onClick = { /* 导航到详情 */ }
        )
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
)
```

---

## 反模式（避免）

### 1. 全局状态

```kotlin
// ❌ 避免
object GlobalState {
    var currentUser: User? = null
}
```

### 2. Context Leaking

```kotlin
// ❌ 错误
class MyActivity : AppCompatActivity() {
    // Activity 持有化
}

// ✅ 正确
class MyActivity : AppCompatActivity() {
    private val viewModel: UserViewModel by viewModels()
}
```

### 3. 主线程阻塞

```kotlin
// ❌ 错误
val result = api.getData() // 主线程网络请求

// ✅ 正确
lifecycleScope.launch {
    val result = api.getData()
}
```

---

## 测试

### Compose UI 测试

```kotlin
class UserScreenTest {
    @get:rule
    fun testUserList_showsCorrectData() {
        composeTest {
            val users = listOf(
                User(1, "Alice"),
                User(2, "Bob")
            )
            
            UserList(
                users = users,
                onUserClick = { /* 测试逻辑 */ }
            )
            
            // 验证
            assert(
                hasText(
                    hasText("Bob", substring = false)
                )
            )
        }
    }
}
```

---

## 安全

### 数据加密

```kotlin
// ✅ 加密存储
EncryptedSharedPreferences.create(context).apply {
    putString("key", value)
}

// 使用时解密
val decrypted = encryptedSharedPreferences.getString("密钥", "默认值")
```

---

## AI 编码检查清单

- [ ] 使用 Kotlin
- [ ] 使用 Jetpack Compose
-   - 状态提升
-   - 避免内存泄漏
- [ ] 使用 MVVM + Repository
- [ ] 使用 Room + Flow
- [ ] 使用 Hilt DI
- [ ] 使用 Retrofit + 协程
- [ ] 使用 Material Design 3
- [ ] 遵循 Material Design 原则
- [ ] 包含测试
- [ ] ProGuard/R8 配置
- [ ] 遵循安全编码规范
    - 输入验证
    - 密码加密
    - SQL 注入防护
    - 权限控制
