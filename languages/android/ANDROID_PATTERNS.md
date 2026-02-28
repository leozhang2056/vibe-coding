# Android Development Patterns & Best Practices

> 基于《Android 开发范例代码大全（第2版）》提炼的开发模式

## 概述

本文档基于《Android 开发范例代码大全（第2版）》（Dave Smith & Jeff Friesen 著）提炼的 Android 开发核心模式和最佳实践。该书包含 727 页内容，涵盖从基础到高级的所有 Android 开发主题。

---

## 一、应用架构

### 1.1 组件（Components）

#### 四大组件

```kotlin
// 1. Activity - 用户界面入口
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }
}

// 2. Service - 后台服务
class MyService : Service() {
    override fun onBind(intent: Intent): IBinder {
        return binder
    }
}

// 3. BroadcastReceiver - 广播接收器
class MyReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context?, intent: Intent?) {
        // 处理广播
    }
}

// 4. ContentProvider - 数据共享
class MyProvider : ContentProvider() {
    override fun query(uri: Uri, projection: Array<String>?, 
                        selection: String?, selectionArgs: Array<String>?,
                        sortOrder: String?): Cursor {
        // 查询逻辑
    }
}
```

### 1.2 Fragment 使用

```kotlin
// ✅ 正确的 Fragment 使用
class MyFragment : Fragment() {
    
    companion object {
        fun newInstance(title: String): MyFragment {
            return MyFragment().apply {
                arguments = Bundle().apply {
                    putString("title", title)
                }
            }
        }
    }
    
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.fragment_my, container, false)
    }
    
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        // 初始化视图
    }
}

// 在 Activity 中使用
supportFragmentManager.beginTransaction()
    .replace(R.id.container, MyFragment.newInstance("Title"))
    .commit()
```

---

## 二、用户界面模式

### 2.1 自定义窗口

```kotlin
// 全屏模式
window.setFlags(
    WindowManager.LayoutParams.FLAG_FULLSCREEN,
    WindowManager.LayoutParams.FLAG_FULLSCREEN
)

// 沉浸式模式（Android 4.4+）
window.decorView.systemUiVisibility = (
    View.SYSTEM_UI_FLAG_LAYOUT_STABLE
    or View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION
    or View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN
    or View.SYSTEM_UI_FLAG_HIDE_NAVIGATION
    or View.SYSTEM_UI_FLAG_FULLSCREEN
    or View.SYSTEM_UI_FLAG_IMMERSIVE_STICKY
)
```

### 2.2 屏幕方向控制

```kotlin
// 在 Manifest 中锁定方向
<activity
    android:name=".MainActivity"
    android:screenOrientation="portrait" />

// 动态锁定方向
requestedOrientation = ActivityInfo.SCREEN_ORIENTATION_PORTRAIT
requestedOrientation = ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE

// 动态解锁
requestedOrientation = ActivityInfo.SCREEN_ORIENTATION_UNSPECIFIED
```

### 2.3 处理配置变更

```kotlin
// 方式 1: 保存状态
override fun onSaveInstanceState(outState: Bundle) {
    super.onSaveInstanceState(outState)
    outState.putString("key", value)
}

override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    savedInstanceState?.getString("key")?.let { value = it }
}

// 方式 2: 使用 ViewModel（推荐）
class MyViewModel : ViewModel() {
    private val _data = MutableLiveData<String>()
    val data: LiveData<String> = _data
}

// 方式 3: 在 Manifest 中禁止重建
<activity
    android:name=".MainActivity"
    android:configChanges="orientation|screenSize" />
```

---

## 三、菜单与对话框

### 3.1 选项菜单（Options Menu）

```kotlin
override fun onCreateOptionsMenu(menu: Menu): Boolean {
    menuInflater.inflate(R.menu.main_menu, menu)
    return true
}

override fun onOptionsItemSelected(item: MenuItem): Boolean {
    return when (item.itemId) {
        R.id.action_settings -> {
            // 处理设置
            true
        }
        else -> super.onOptionsItemSelected(item)
    }
}
```

### 3.2 上下文菜单（Context Menu）

```kotlin
registerForContextMenu(listView)

override fun onCreateContextMenu(
    menu: ContextMenu,
    v: View,
    menuInfo: ContextMenu.ContextMenuInfo?
) {
    menuInflater.inflate(R.menu.context_menu, menu)
}

override fun onContextItemSelected(item: MenuItem): Boolean {
    return when (item.itemId) {
        R.id.action_delete -> {
            // 删除操作
            true
        }
        else -> super.onContextItemSelected(item)
    }
}
```

### 3.3 对话框（Dialog）

```kotlin
// AlertDialog
AlertDialog.Builder(this)
    .setTitle("确认")
    .setMessage("确定要删除吗？")
    .setPositiveButton("确定") { dialog, which ->
        // 确认操作
    }
    .setNegativeButton("取消", null)
    .show()

// ProgressDialog（已弃用，使用 ProgressBar）
// ✅ 使用 ProgressBar 替代
val progressDialog = ProgressDialog(this).apply {
    setMessage("加载中...")
    setCancelable(false)
}
progressDialog.show()

// 或者使用布局中的 ProgressBar
<ProgressBar
    android:id="@+id/progressBar"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:visibility="gone" />
```

---

## 四、多分辨率适配

### 4.1 资源限定符

```
res/
  layout/              # 默认布局
  layout-land/         # 横屏布局
  layout-sw600dp/      # 最小宽度 600dp（平板）
  layout-large/        # 大屏幕
  drawable/            # 默认图片
  drawable-hdpi/       # 高密度 (~240dpi)
  drawable-xhdpi/      # 超高密度 (~320dpi)
  drawable-xxhdpi/     # 超超高密度 (~480dpi)
  values/              # 默认值
  values-sw600dp/      # 平板值
```

### 4.2 屏幕尺寸适配

```xml
<!-- 使用 dp 而不是 px -->
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="16sp" />
    
<!-- 使用 wrap_content 和 match_parent -->
<ImageView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:scaleType="centerCrop" />
```

### 4.3 图标适配

```
drawable-mdpi/    (48x48px)
drawable-hdpi/    (72x72px)
drawable-xhdpi/   (96x96px)
drawable-xxhdpi/  (144x144px)
drawable-xxxhdpi/ (192x192px)
```

---

## 五、数据持久化

### 5.1 SharedPreferences

```kotlin
// 保存数据
val sharedPref = getSharedPreferences("MyPrefs", Context.MODE_PRIVATE)
with(sharedPref.edit()) {
    putString("key", "value")
    putInt("count", 100)
    putBoolean("flag", true)
    apply()  // 或 commit() 用于同步
}

// 读取数据
val value = sharedPref.getString("key", "default")
val count = sharedPref.getInt("count", 0)
val flag = sharedPref.getBoolean("flag", false)
```

### 5.2 文件存储

```kotlin
// 内部存储
val fileName = "myfile"
val content = "Hello World"

// 写入
openFileOutput(fileName, Context.MODE_PRIVATE).use {
    it.write(content.toByteArray())
}

// 读取
openFileInput(fileName).use {
    val text = it.bufferedReader().readText()
}

// 外部存储（需要权限）
if (checkSelfPermission(Manifest.permission.WRITE_EXTERNAL_STORAGE) 
    == PackageManager.PERMISSION_GRANTED) {
    val file = File(getExternalFilesDir(null), "myfile.txt")
    file.writeText(content)
}
```

### 5.3 SQLite 数据库

```kotlin
// 数据库帮助类
class MyDbHelper(context: Context) : SQLiteOpenHelper(
    context,
    DATABASE_NAME,
    null,
    DATABASE_VERSION
) {
    companion object {
        private const val DATABASE_NAME = "mydb.db"
        private const val DATABASE_VERSION = 1
    }
    
    override fun onCreate(db: SQLiteDatabase) {
        db.execSQL("""
            CREATE TABLE users (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                name TEXT NOT NULL,
                email TEXT UNIQUE
            )
        """)
    }
    
    override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
        db.execSQL("DROP TABLE IF EXISTS users")
        onCreate(db)
    }
}

// 使用数据库
val dbHelper = MyDbHelper(this)
val db = dbHelper.writableDatabase

// 插入
val values = ContentValues().apply {
    put("name", "张三")
    put("email", "zhangsan@example.com")
}
db.insert("users", null, values)

// 查询
val cursor = db.query(
    "users",
    arrayOf("id", "name", "email"),
    null, null, null, null, null
)
while (cursor.moveToNext()) {
    val id = cursor.getInt(cursor.getColumnIndexOrThrow("id"))
    val name = cursor.getString(cursor.getColumnIndexOrThrow("name"))
    // 处理数据
}
cursor.close()
```

### 5.4 Room 数据库（推荐）

```kotlin
// Entity
@Entity(tableName = "users")
data class User(
    @PrimaryKey(autoGenerate = true)
    val id: Long = 0,
    val name: String,
    val email: String
)

// DAO
@Dao
interface UserDao {
    @Insert
    suspend fun insert(user: User): Long
    
    @Query("SELECT * FROM users WHERE id = :id")
    suspend fun getById(id: Long): User?
    
    @Query("SELECT * FROM users")
    fun getAll(): LiveData<List<User>>
    
    @Update
    suspend fun update(user: User)
    
    @Delete
    suspend fun delete(user: User)
}

// Database
@Database(entities = [User::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
}

// 使用
val db = Room.databaseBuilder(
    applicationContext,
    AppDatabase::class.java,
    "app-database"
).build()

// 在协程中使用
lifecycleScope.launch {
    val user = User(name = "张三", email = "zhangsan@example.com")
    db.userDao().insert(user)
    val users = db.userDao().getAll()
}
```

---

## 六、网络编程

### 6.1 HttpURLConnection（基础）

```kotlin
// GET 请求
thread {
    val url = URL("https://api.example.com/data")
    val connection = url.openConnection() as HttpURLConnection
    try {
        connection.requestMethod = "GET"
        connection.connectTimeout = 5000
        connection.readTimeout = 5000
        
        if (connection.responseCode == HttpURLConnection.HTTP_OK) {
            val text = connection.inputStream.bufferedReader().readText()
            // 处理响应
        }
    } finally {
        connection.disconnect()
    }
}

// POST 请求
thread {
    val url = URL("https://api.example.com/data")
    val connection = url.openConnection() as HttpURLConnection
    try {
        connection.requestMethod = "POST"
        connection.doOutput = true
        connection.setRequestProperty("Content-Type", "application/json")
        
        connection.outputStream.use {
            it.write(json.toByteArray())
        }
        
        if (connection.responseCode == HttpURLConnection.HTTP_OK) {
            val response = connection.inputStream.bufferedReader().readText()
            // 处理响应
        }
    } finally {
        connection.disconnect()
    }
}
```

### 6.2 Retrofit（推荐）

```kotlin
// API 接口
interface ApiService {
    @GET("users/{id}")
    suspend fun getUser(@Path("id") id: Long): Response<User>
    
    @POST("users")
    suspend fun createUser(@Body user: User): Response<User>
    
    @GET("users")
    suspend fun getUsers(): Response<List<User>>
}

// Retrofit 实例
val retrofit = Retrofit.Builder()
    .baseUrl("https://api.example.com/")
    .addConverterFactory(MoshiConverterFactory.create())
    .build()
    
val apiService = retrofit.create(ApiService::class.java)

// 在协程中使用
lifecycleScope.launch {
    try {
        val response = apiService.getUser(1)
        if (response.isSuccessful) {
            val user = response.body()
            // 处理用户数据
        }
    } catch (e: Exception) {
        // 处理错误
    }
}
```

---

## 七、后台任务

### 7.1 Service

```kotlin
// 前台服务
class MyForegroundService : Service() {
    
    private val CHANNEL_ID = "foreground_service_channel"
    
    override fun onCreate() {
        super.onCreate()
        createNotificationChannel()
    }
    
    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        val notification = NotificationCompat.Builder(this, CHANNEL_ID)
            .setContentTitle("前台服务")
            .setContentText("正在运行...")
            .setSmallIcon(R.drawable.ic_notification)
            .build()
        
        startForeground(1, notification)
        
        // 执行任务
        doWork()
        
        return START_STICKY
    }
    
    private fun createNotificationChannel() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val channel = NotificationChannel(
                CHANNEL_ID,
                "前台服务",
                NotificationManager.IMPORTANCE_DEFAULT
            )
            val manager = getSystemService(NotificationManager::class.java)
            manager.createNotificationChannel(channel)
        }
    }
    
    private fun doWork() {
        // 执行后台任务
    }
    
    override fun onBind(intent: Intent?): IBinder? = null
}
```

### 7.2 WorkManager（推荐）

```kotlin
// 定义 Worker
class MyWorker(context: Context, params: WorkerParameters) : Worker(context, params) {
    
    override fun doWork(): Result {
        // 执行任务
        return try {
            // 任务逻辑
            Result.success()
        } catch (e: Exception) {
            Result.failure()
        }
    }
}

// 调度一次性任务
val workRequest = OneTimeWorkRequestBuilder<MyWorker>()
    .setInitialDelay(10, TimeUnit.SECONDS)
    .build()

WorkManager.getInstance(context).enqueue(workRequest)

// 调度周期性任务
val periodicWork = PeriodicWorkRequestBuilder<MyWorker>(
    15, TimeUnit.MINUTES  // 最小间隔 15 分钟
).build()

WorkManager.getInstance(context).enqueue(periodicWork)
```

---

## 八、权限管理

### 8.1 运行时权限

```kotlin
// 检查权限
if (ContextCompat.checkSelfPermission(this, Manifest.permission.CAMERA)
    != PackageManager.PERMISSION_GRANTED) {
    
    // 请求权限
    ActivityCompat.requestPermissions(
        this,
        arrayOf(Manifest.permission.CAMERA),
        REQUEST_CAMERA
    )
} else {
    // 权限已授予
    openCamera()
}

// 处理权限结果
override fun onRequestPermissionsResult(
    requestCode: Int,
    permissions: Array<out String>,
    grantResults: IntArray
) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults)
    
    when (requestCode) {
        REQUEST_CAMERA -> {
            if (grantResults.isNotEmpty() && 
                grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                openCamera()
            } else {
                Toast.makeText(this, "相机权限被拒绝", Toast.LENGTH_SHORT).show()
            }
        }
    }
}
```

### 8.2 使用 EasyPermissions 库

```kotlin
// 添加依赖
// implementation 'pub.devrel:easypermissions:3.0.0'

// 请求权限
@AfterPermissionGranted(REQUEST_CAMERA)
private fun openCamera() {
    if (EasyPermissions.hasPermissions(this, Manifest.permission.CAMERA)) {
        // 权限已授予
        startCamera()
    } else {
        // 请求权限
        EasyPermissions.requestPermissions(
            this,
            "需要相机权限",
            REQUEST_CAMERA,
            Manifest.permission.CAMERA
        )
    }
}

override fun onRequestPermissionsResult(
    requestCode: Int,
    permissions: Array<out String>,
    grantResults: IntArray
) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults)
    EasyPermissions.onRequestPermissionsResult(requestCode, permissions, grantResults, this)
}
```

---

## 九、性能优化

### 9.1 避免内存泄漏

```kotlin
// ❌ 错误 - 非静态内部类持有外部类引用
class MyActivity : AppCompatActivity() {
    private inner class MyTask : AsyncTask<Void, Void, Void>() {
        override fun doInBackground(vararg params: Void): Void? {
            // 任务执行
            return null
        }
    }
}

// ✅ 正确 - 使用静态内部类 + WeakReference
class MyActivity : AppCompatActivity() {
    private static class MyTask(activity: MyActivity) : AsyncTask<Void, Void, Void>() {
        private val activityRef = WeakReference(activity)
        
        override fun doInBackground(vararg params: Void): Void? {
            val activity = activityRef.get()
            activity?.let {
                // 使用 activity
            }
            return null
        }
    }
}

// ✅ 更好 - 使用协程
class MyActivity : AppCompatActivity() {
    private val job = SupervisorJob()
    private val scope = CoroutineScope(Dispatchers.Main + job)
    
    private fun doWork() {
        scope.launch {
            // 在这里使用 this@MyActivity 是安全的
            // 协程会在 Activity 销毁时自动取消
        }
    }
    
    override fun onDestroy() {
        super.onDestroy()
        job.cancel()
    }
}
```

### 9.2 ViewHolder 模式

```kotlin
// RecyclerView Adapter
class MyAdapter(private val items: List<Item>) : RecyclerView.Adapter<MyAdapter.ViewHolder>() {
    
    class ViewHolder(view: View) : RecyclerView.ViewHolder(view) {
        val titleTextView: TextView = view.findViewById(R.id.title)
        val descriptionTextView: TextView = view.findViewById(R.id.description)
    }
    
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(R.layout.item_layout, parent, false)
        return ViewHolder(view)
    }
    
    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val item = items[position]
        holder.titleTextView.text = item.title
        holder.descriptionTextView.text = item.description
    }
    
    override fun getItemCount() = items.size
}
```

### 9.3 图片加载优化

```kotlin
// 使用 Glide 或 Coil

// Glide
Glide.with(this)
    .load imageUrl
    .placeholder(R.drawable.placeholder)
    .error(R.drawable.error)
    .diskCacheStrategy(DiskCacheStrategy.ALL)
    .into(imageView)

// Coil（推荐用于 Kotlin）
imageView.load(imageUrl) {
    placeholder(R.drawable.placeholder)
    error(R.drawable.error)
    crossfade(true)
    transformations(RoundedCornersTransformation(16f))
}
```

---

## 十、测试

### 10.1 单元测试

```kotlin
// 使用 JUnit 和 Mockito
@RunWith(MockitoJUnitRunner::class)
class MyViewModelTest {
    
    @Mock
    private lateinit var repository: MyRepository
    
    @InjectMocks
    private lateinit var viewModel: MyViewModel
    
    @Test
    fun `should return user when id is valid`() = runTest {
        // Given
        val expectedUser = User(id = 1, name = "张三")
        whenever(repository.getUser(1)).thenReturn(expectedUser)
        
        // When
        val result = viewModel.getUser(1)
        
        // Then
        assertEquals(expectedUser, result)
    }
}
```

### 10.2 UI 测试

```kotlin
// 使用 Espresso
@RunWith(AndroidJUnit4::class)
class LoginActivityTest {
    
    @get:Rule
    val activityRule = ActivityScenarioRule(LoginActivity::class.java)
    
    @Test
    fun `should show error when email is empty`() {
        // 输入空邮箱
        onView(withId(R.id.emailEditText))
            .perform(typeText(""))
        
        // 点击登录
        onView(withId(R.id.loginButton))
            .perform(click())
        
        // 验证错误提示
        onView(withId(R.id.errorTextView))
            .check(matches(withText(R.string.email_required)))
    }
}
```

---

## 十一、最佳实践总结

### 11.1 架构模式

```kotlin
// MVVM + Repository 模式

// 1. Model
data class User(val id: Long, val name: String, val email: String)

// 2. Repository
class UserRepository(private val apiService: ApiService, private val database: AppDatabase) {
    
    suspend fun getUser(id: Long): User {
        // 先从数据库读取
        val cached = database.userDao().getById(id)
        if (cached != null) return cached
        
        // 从网络获取
        val user = apiService.getUser(id).body()!!
        
        // 保存到数据库
        database.userDao().insert(user)
        
        return user
    }
}

// 3. ViewModel
class UserViewModel(private val repository: UserRepository) : ViewModel() {
    
    private val _user = MutableLiveData<User>()
    val user: LiveData<User> = _user
    
    fun loadUser(id: Long) {
        viewModelScope.launch {
            val result = repository.getUser(id)
            _user.value = result
        }
    }
}

// 4. View (Activity/Fragment)
class UserActivity : AppCompatActivity() {
    
    private val viewModel: UserViewModel by viewModels()
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_user)
        
        viewModel.user.observe(this) { user ->
            // 更新 UI
            nameTextView.text = user.name
        }
        
        viewModel.loadUser(userId)
    }
}
```

### 11.2 依赖注入（Hilt）

```kotlin
// Application 类
@HiltAndroidApp
class MyApplication : Application()

// Module
@Module
@InstallIn(SingletonComponent::class)
object AppModule {
    
    @Provides
    @Singleton
    fun provideApiService(): ApiService {
        return Retrofit.Builder()
            .baseUrl("https://api.example.com/")
            .addConverterFactory(MoshiConverterFactory.create())
            .build()
            .create(ApiService::class.java)
    }
    
    @Provides
    @Singleton
    fun provideDatabase(@ApplicationContext context: Context): AppDatabase {
        return Room.databaseBuilder(
            context,
            AppDatabase::class.java,
            "app-database"
        ).build()
    }
}

// 注入依赖
@AndroidEntryPoint
class MainActivity : AppCompatActivity() {
    
    @Inject
    lateinit var viewModelFactory: UserViewModelFactory
    
    private val viewModel: UserViewModel by viewModels { viewModelFactory }
}
```

### 11.3 协程使用

```kotlin
// 协程作用域选择

// 1. viewModelScope - ViewModel 中使用
viewModelScope.launch {
    // 自动在 ViewModel 清除时取消
}

// 2. lifecycleScope - Activity/Fragment 中使用
lifecycleScope.launch {
    // 自动在生命周期销毁时取消
}

// 3. 自定义作用域
class MyRepository {
    private val scope = CoroutineScope(SupervisorJob() + Dispatchers.Default)
    
    fun doWork() {
        scope.launch {
            // 在 Repository 中执行异步任务
        }
    }
    
    fun cleanup() {
        scope.cancel()
    }
}
```

---

## 十二、检查清单

### 应用架构

- [ ] 使用 MVVM 或 MVI 架构
- [ ] Repository 模式分离数据源
- [ ] 使用 Hilt 进行依赖注入
- [ ] 遵循单一职责原则

### UI 开发

- [ ] 使用 ConstraintLayout 或 Compose
- [ ] 适配不同屏幕尺寸
- [ ] 使用样式和主题
- [ ] 遵循 Material Design 规范

### 数据处理

- [ ] 使用 Room 数据库
- [ ] 使用 Retrofit 进行网络请求
- [ ] 使用 Coil/Glide 加载图片
- [ ] 使用 DataStore 存储简单数据

### 异步任务

- [ ] 使用 Kotlin 协程
- [ ] 使用 WorkManager 处理后台任务
- [ ] 使用 Flow 处理数据流

### 性能优化

- [ ] 避免 memory leak
- [ ] 使用 ViewHolder 模式
- [ ] 启用 R8 混淆
- [ ] 使用 ProGuard 优化

### 测试

- [ ] 编写单元测试
- [ ] 编写 UI 测试
- [ ] 使用测试 doubles (Mock/Fake)

---

## 总结

Android 开发的核心原则：

1. **响应式设计**：适配不同设备和屏幕尺寸
2. **生命周期感知**：正确处理 Activity/Fragment 生命周期
3. **异步优先**：使用协程处理耗时操作
4. **数据驱动**：使用 LiveData/Flow 构建响应式 UI
5. **模块化**：使用多模块架构分离功能
6. **测试覆盖**：单元测试 + UI 测试确保质量

遵循这些模式和最佳实践，可以构建高质量、可维护的 Android 应用。
