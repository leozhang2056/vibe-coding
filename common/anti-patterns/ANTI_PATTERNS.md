# 反模式（Anti-Patterns）

> 常见的编程错误，应该避免

## 代码层面

### 1. 魔法数字（Magic Numbers）

> 硬编码的数字，没有语义

```python
# ❌ 反模式
def calculate_discount(price):
    return price * 0.85  # 0.85 是什么？

# ✅ 正确
DISCOUNT_RATE = 0.15
def calculate_discount(price):
    return price * (1 - DISCOUNT_RATE)
```

---

### 2. 复制粘贴编程

> 重复代码而非提取函数

```python
# ❌ 反模式
def process_user_a():
    if user.age >= 18:
        print("成年")
        send_email(user)
        save_to_db(user)

def process_user_b():
    if user.age >= 18:
        print("成年")
        send_email(user)
        save_to_db(user)

# ✅ 正确
def process_adult_user(user):
    print("成年")
    send_email(user)
    save_to_db(user)
```

---

### 3. 嵌套地狱（Callback Hell）

> 过度嵌套的回调

```javascript
// ❌ 反模式
getData(function(a) {
  getMoreData(a, function(b) {
    getMoreData(b, function(c) {
      getMoreData(c, function(d) {
        // ...
      });
    });
  });
});

// ✅ 正确（async/await）
const a = await getData();
const b = await getMoreData(a);
const c = await getMoreData(b);
const d = await getMoreData(c);
```

---

### 4. 过早优化（Premature Optimization）

> 在性能问题出现前优化

```python
# ❌ 反模式
def process_items(items):
    # 过度使用缓存，数据量小时没必要
    cache = {}
    results = []
    for item in items:
        if item.id not in cache:
            cache[item.id] = expensive_calculation(item)
        results.append(cache[item.id])
    return results

# ✅ 正确（先简单实现）
def process_items(items):
    return [expensive_calculation(item) for item in items]
```

---

## 架构层面

### 5. 大泥球（Big Ball of Mud）

> 没有清晰架构，代码混乱

```python
# ❌ 反模式（所有代码混在一起）
def handle_request(request):
    # 解析参数
    params = parse(request)
    
    # 业务逻辑
    if params['type'] == 'user':
        db.query("SELECT * FROM users")
    elif params['type'] == 'product':
        db.query("SELECT * FROM products")
    
    # HTML 生成
    html = "<html>" + str(result) + "</html>"
    return html

# ✅ 正确（分层架构）
# Controller 层
def handle_request(request):
    params = parse(request)
    result = use_case.execute(params)
    return presenter.present(result)

# Use Case 层
class UserUseCase:
    def execute(self, params):
        user = repository.find(params['id'])
        return user
```

---

### 6. 循环依赖（Circular Dependency）

> 模块之间相互依赖

```python
# ❌ 反模式
# module_a.py
from module_b import function_b

def function_a():
    function_b()

# module_b.py
from module_a import function_a  # 循环依赖！

def function_b():
    function_a()

# ✅ 正确（依赖倒置）
# shared.py（抽象层）
from abc import ABC, abstractmethod

class Handler(ABC):
    @abstractmethod
    def handle(self):
        pass

# module_a.py
class HandlerA(Handler):
    def handle(self):
        pass

# module_b.py
class HandlerB(Handler):
    def handle(self):
        pass
```

---

## 并发层面

### 7. 竞态条件（Race Condition）

> 多线程/进程访问共享资源时未加锁

```python
# ❌ 反模式
counter = 0

def increment():
    global counter
    counter += 1  # 非原子操作，不安全

# ✅ 正确
from threading import Lock

counter = 0
lock = Lock()

def increment():
    global counter
    with lock:
        counter += 1
```

---

### 8. 死锁（Deadlock）

> 多个线程互相等待对方释放锁

```python
# ❌ 反模式
lock1 = Lock()
lock2 = Lock()

def thread1():
    with lock1:
        time.sleep(0.1)
        with lock2:  # 可能死锁
            pass

def thread2():
    with lock2:
        time.sleep(0.1)
        with lock1:  # 可能死锁
            pass

# ✅ 正确（锁顺序一致）
def thread1():
    with lock1:
        with lock2:
            pass

def thread2():
    with lock1:  # 始终先获取 lock1
        with lock2:
            pass
```

---

## 数据层面

### 9. N+1 查询

> 循环中查询数据库

```python
# ❌ 反模式
users = db.query("SELECT * FROM users")
for user in users:
    orders = db.query(f"SELECT * FROM orders WHERE user_id = {user.id}")

# ✅ 正确（一次查询）
users_orders = db.query("""
    SELECT u.*, o.* 
    FROM users u 
    LEFT JOIN orders o ON o.user_id = u.id
""")
```

---

### 10. 过度获取数据（Over-fetching）

> 获取不需要的数据

```python
# ❌ 反模式
users = db.query("SELECT * FROM users")  # 获取所有列
for user in users:
    print(user.name)  # 只需要 name

# ✅ 正确
users = db.query("SELECT id, name FROM users")  # 只获取需要的列
for user in users:
    print(user.name)
```

---

## 测试层面

### 11. 测试覆盖实现细节

> 测试应该关注行为，而非实现

```python
# ❌ 反模式
def test_calculate_discount():
    user = User()
    user._discount = 0.15  # 测试私有实现细节
    assert user.price == 85

# ✅ 正确
def test_calculate_discount():
    user = User(discount_rate=0.15)
    assert user.calculate_price(100) == 85  # 测试公开行为
```

---

### 12. 脆弱测试（Flaky Tests）

> 依赖外部状态或时间的测试

```python
# ❌ 反模式
def test_user_age():
    user = User(birth_year=1990)
    assert user.age == datetime.now().year - 1990  # 依赖当前时间

# ✅ 正确
def test_user_age():
    user = User(birth_year=1990)
    assert user.get_age_in_year(2025) == 35  # 不依赖当前时间
```

---

## AI 编码检查清单

- [ ] 没有硬编码的魔法数字
- [ ] 没有重复代码
- [ ] 嵌套层级不超过 3 层
- [ ] 没有过早优化
- [ ] 依赖关系是单向的
- [ ] 共享资源有锁保护
- [ ] 数据库查询已优化
- [ ] 测试关注行为而非实现
