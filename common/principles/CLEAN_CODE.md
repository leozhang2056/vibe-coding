# Clean Code - 代码整洁之道

> 基于 Robert C. Martin 的《Clean Code》

## 命名

### 有意义的命名

**原则：**
- 名字应该解释「为什么」存在，做什么，怎么用
- 避免使用无意义的变量名（data, info, temp）

**示例：**

```python
# ❌ 不好的命名
d = 5  # 天是什么？
lst = [1, 2, 3]  # 列表里是什么？

# ✅ 好的命名
days_until_expiry = 5
prime_numbers = [1, 2, 3]
```

### 避免误导

- 不要使用相似名称（UserController vs UserManager）
- 避免拼写错误（constnat → constant）

### 做有意义的区分

```python
# ❌ 无意义区分
product1, product2, product3

# ✅ 有意义区分
pending_product, processing_product, completed_product
```

### 使用可读的名称

```python
# ❌ 难读
def calc(d):
    pass

# ✅ 易读
def calculate_discount(days_until_expiry):
    pass
```

---

## 函数

### 函数应该短小

**原则：**
- 函数不应超过 20 行
- 一个函数只做一件事

### 只做一件事

```python
# ❌ 做了太多事
def process_user(user):
    # 验证
    if not user.email:
        return False
    # 保存
    db.save(user)
    # 发邮件
    email.send(user)
    return True

# ✅ 每个函数只做一件事
def validate_user(user):
    return bool(user.email)

def save_user(user):
    db.save(user)

def notify_user(user):
    email.send(user)

def process_user(user):
    if validate_user(user):
        save_user(user)
        notify_user(user)
```

### 参数数量

- 最好 0 个
- 最多 3 个
- 超过 3 个考虑封装成对象

```python
# ❌ 参数太多
def create_user(name, email, age, address, phone, password):
    pass

# ✅ 封装成对象
@dataclass
class UserDTO:
    name: str
    email: str
    age: int
    address: str
    phone: str
    password: str

def create_user(dto: UserDTO):
    pass
```

### 无副作用

- 函数不应该修改传入参数（除非明确说明）
- 避免隐式依赖全局状态

---

## 注释

### 代码即文档

**原则：**
- 好的代码不需要注释
- 注释是「代码表达失败」的补救

### 不要用注释解释「是什么」

```python
# ❌ 无用注释
# 设置计数器为 0
count = 0

# ✅ 好的代码无需注释
initial_count = 0
```

### 解释「为什么」才需要注释

```python
# 使用快速排序因为数据已经部分排序
# 归并排序的 O(n log n) 在这里不必要
def sort_data(data):
    return quicksort(data)
```

### TODO 注释

```python
# TODO: 添加缓存以提高性能
def fetch_data():
    pass
```

---

## 对象与数据结构

### 数据结构（被动）

```python
@dataclass
class Point:
    x: float
    y: float
    # 只暴露数据，无行为
```

### 对象（主动）

```python
class Point:
    def __init__(self, x, y):
        self._x = x
        self._y = y
    
    def distance_to(self, other):
        # 暴露行为，隐藏数据
        return math.sqrt((self._x - other._x)**2 + (self._y - other._y)**2)
```

### Law of Demeter (最少知识原则)

```python
# ❌ 违反最少知识原则
user.get_address().get_city().get_country()

# ✅ 遵循最少知识原则
user.get_country()
```

---

## 错误处理

### 优先使用异常而非返回错误码

```python
# ❌ 返回错误码
def divide(a, b):
    if b == 0:
        return -1  # -1 表示错误
    return a / b

# ✅ 使用异常
def divide(a, b):
    if b == 0:
        raise ValueError("除数不能为零")
    return a / b
```

### 别返回 NULL

```python
# ❌ 返回 None
def find_user(id):
    user = db.query(id)
    if not user:
        return None
    return user

# ✅ 返回空对象或抛出异常
def find_user(id):
    user = db.query(id)
    if not user:
        raise UserNotFound(f"用户 {id} 不存在")
    return user
```

---

## 边界

### 探索边界

```python
# 使用第三方库时，用适配器封装
class PaymentGatewayAdapter:
    def __init__(self, gateway):
        self.gateway = gateway
    
    def charge(self, amount):
        # 隔离第三方 API
        return self.gateway.process_payment(amount)
```

---

## 单元测试

### FIRST 原则

- **F**ast - 快速
- **I**ndependent - 独立
- **R**epeatable - 可重复
- **S**elf-Validating - 自我验证
- **T**imely - 及时

### 测试命名

```python
def test_user_created_successfully():
    pass

def test_user_creation_fails_with_invalid_email():
    pass
```

---

## AI 编码检查清单

编码时，确保代码符合以下检查：

- [ ] 变量/函数/类名有意义
- [ ] 函数不超过 20 行
- [ ] 函数只做一件事
- [ ] 函数参数不超过 3 个
- [ ] 没有无用注释
- [ ] 用异常而非错误码
- [ ] 不返回 NULL（用空对象或异常）
- [ ] 每个函数都有单元测试
