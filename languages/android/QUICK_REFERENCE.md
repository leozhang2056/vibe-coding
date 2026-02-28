# Android 快速参考

> Android 开发常用代码片段

## Activity

```kotlin
// 基础 Activity
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }
}
```

## Fragment

```kotlin
// 基础 Fragment
class MyFragment : Fragment() {
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.fragment_my, container, false)
    }
}
```

## RecyclerView Adapter

```kotlin
class UserAdapter(
    private val users: List<User>,
    private val onClick: (User) -> Unit
) : RecyclerView.Adapter<UserAdapter.ViewHolder>() {

    class ViewHolder(view: View) : RecyclerView.ViewHolder(view) {
        val nameTextView: TextView = view.findViewById(R.id.name_text)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(R.layout.item_user, parent, false)
        return ViewHolder(view)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val user = users[position]
        holder.nameTextView.text = user.name
        holder.itemView.setOnClickListener {
            onClick(user)
        }
    }

    override fun getItemCount() = users.size
}
```

## 网络请求（Retrofit）

```kotlin
// API 接口
interface ApiService {
    @GET("users")
    suspend fun getUsers(): Response<List<User>>
    
    @POST("users")
    suspend fun createUser(@Body user: User): Response<User>
}

// 使用
val retrofit = Retrofit.Builder()
    .baseUrl("https://api.example.com/")
    .addConverterFactory(MoshiConverterFactory.create())
    .build()
```

## 数据库（Room）

```kotlin
@Entity
data class User(
    @PrimaryKey val id: Int,
    @ColumnInfo(name = "name") val name: String
)

@Dao
interface UserDao {
    @Query("SELECT * FROM users")
    fun getAll(): List<User>
    
    @Insert
    fun insert(user: User): Long
}

@Database(entities = [User::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
}
```

## 协程

```kotlin
// 启动协程
lifecycleScope.launch {
    // 网络请求
    val result = api.getData()
    
    // 数据库操作
    database.userDao().insert(user)
    
    // UI 更新
    withContext(Dispatchers.Main) {
        textView.text = "完成"
    }
}
```

## Jetpack Compose

```kotlin
@Composable
fun MyScreen() {
    var text by remember { mutableStateOf("") }
    
    Column(
        modifier = Modifier.padding(16.dp),
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        TextField(
            value = text,
            onValueChange = { text = it },
            modifier = Modifier.fillMaxWidth()
        )
        Button(
            onClick = { text = "Hello" },
            modifier = Modifier.padding(top = 16.dp)
        )
    }
}
```

## SharedPreferences

```kotlin
// 首选项
val context = LocalContext.current
val prefs = context.getSharedPreferences("MyPrefs", Context.MODE_PRIVATE)

// 保存
prefs.edit()
    .putString("key", "value")
    .apply()

// 读取
val value = prefs.getString("key", "default")
```

## Intent 导航

```kotlin
// 启动 Activity
val intent = Intent(context, MainActivity::class.java)
startActivity(intent)

// 启动 Activity 并返回结果
startActivityForResult(intent, REQUEST_CODE)

// 启动 Service
val intent = Intent(context, MyService::class.java)
ContextCompat.startForegroundService(context, intent)
```

## 权限请求

```kotlin
// 检查权限
if (ContextCompat.checkSelfPermission(
        requireContext(),
        Manifest.permission.CAMERA
    ) != PackageManager.PERMISSION_GRANTED
) {
    requestPermissions(
        arrayOf(Manifest.permission.CAMERA),
        REQUEST_CODE_CAMERA
    )
}

// 处理权限结果
override fun onRequestPermissionsResult(
    requestCode: Int,
    permissions: Array<out String],
    grantResults: IntArray
) {
    if (requestCode == REQUEST_CODE_CAMERA) {
        if (grantResults.isNotEmpty() &&
            grantResults[0] == PackageManager.PERMISSION_GRANTED
        ) {
            // 权限已授予
        }
    }
}
```

---

## 常用库推荐

| 库 | 用途 |
|------|------|
| **Jetpack Compose** | UI 框架 |
| **Retrofit** | 网络请求 |
| **Room** | 本地数据库 |
| **Hilt** | 依赖注入 |
| **Glide** | 图片加载 |
| ** Coil** | 图片加载（Kotlin） |
| **Moshi** | JSON 解析 |
| **Timber** | 日志 |

---

## AI 编码检查清单

- [ ] 使用 Kotlin（非 Java）
- [ ] 使用 Jetpack Compose（非 XML）
- [ ] 使用协程处理异步
-   - StateFlow/Flow
- [ ] 使用 MVVM + Repository
- [ ] 使用 Room 数据库
-   - Flow 返回数据
- [ ] 使用 Hilt 依赖注入
- [ ] 使用 Material Design 3
- [ ] 遵循 Material Design 原则
