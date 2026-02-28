# Python 编码规范

> PEP 8 + Pythonic 实践

## 代码风格

### 遵循 PEP 8

```python
# ✓ 正确
def calculate_discount(price: float, discount_rate: float) -> float:
    """计算折扣后的价格"""
    return price * (1 - discount_rate)

# ✗ 错误
def calculateDiscount(price, discountRate):
    return price * (1 - discountRate)
```

### 命名规范

| 类型 | 规范 | 示例 |
|------|------|------|
| 变量/函数 | snake_case | `user_name`, `get_data()` |
| 类 | PascalCase | `UserController`, `UserService` |
| 常量 | UPPER_SNAKE_CASE | `MAX_CONNECTIONS`, `DEFAULT_TIMEOUT` |
| 私有成员 | 前缀下划线 | `_internal_method`, `private_var` |

### 行宽

- 最大 79 字符
- 换行时用反斜杠或括号

```python
# ✓ 正确
result = (some_very_long_function_name(arg1, arg2, arg3)
          .another_method(arg4, arg5))

# ✓ 正确
result = some_very_long_function_name(
    arg1, arg2, arg3
).another_method(arg4, arg5)
```

---

## Pythonic 写法

### 列表推导

```python
# ✓ Pythonic
squares = [x**2 for x in range(10)]

# ✗ 不 Pythonic
squares = []
for x in range(10):
    squares.append(x**2)
```

### 上下文管理器（with 语句）

```python
# ✓ 正确
with open('file.txt', 'r') as f:
    content = f.read()

# ✗ 错误
f = open('file.txt', 'r')
content = f.read()
f.close()
```

### 枚举

```python
# ✓ 正确
for index, item in enumerate(items):
    print(f"{index}: {item}")

# ✗ 不 Pythonic
for i in range(len(items)):
    print(f"{i}: {items[i]}")
```

### 字典操作

```python
# ✓ Pythonic
value = data.get('key', default_value)

# ✓ 合并字典
merged = {**dict1, **dict2}

# ✓ 字典推导
squared = {x: x**2 for x in range(5)}
```

---

## 类型提示

### 函数签名

```python
from typing import List, Dict, Optional

def get_users(age_min: int = 0) -> List[Dict[str, any]]:
    """获取用户列表"""
    return query_users(age_min)
```

### 类属性

```python
from dataclasses import dataclass

@dataclass
class User:
    name: str
    email: str
    age: Optional[int] = None
```

---

## 错误处理

### 具体异常

```python
# ✓ 正确
try:
    user = get_user(user_id)
except UserNotFoundError:
    logger.error("用户不存在")
except DatabaseError:
    logger.error("数据库错误")

# ✗ 错误
try:
    user = get_user(user_id)
except:
    pass
```

### EAFP (Easier to Ask for Forgiveness)

```python
# ✓ Pythonic: EAFP
try:
    value = dict[key]
except KeyError:
    value = default_value

# ✗ LBYL (Look Before You Leap)
if key in dict:
    value = dict[key]
else:
    value = default_value
```

---

## 模块组织

### 相对导入

```
project/
├── __init__.py
├── models/
│   ├── __init__.py
│   └── user.py
└── services/
    ├── __init__.py
    └── user_service.py
```

```python
# services/user_service.py
from ..models.user import User  # 相对导入
from .database import Database
```

### 避免 import *

```python
# ✗ 错误
from os import *

# ✓ 正确
import os
from os.path import join
```

---

## 生成器

### 惰性求值

```python
# ✓ 生成器
def read_large_file(file_path):
    with open(file_path) as f:
        for line in f:
            yield line.strip()

# 使用
for line in read_large_file('huge.txt'):
    process(line)
```

---

## 装饰器

### 使用标准库装饰器

```python
from functools import lru_cache, wraps
import time

# 缓存
@lru_cache(maxsize=128)
def expensive_function(n):
    return n ** 2

# 计时
def timer(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        print(f"{func.__name__} took {time.time() - start}s")
        return result
    return wrapper

@timer
def slow_function():
    time.sleep(1)
```

---

## 并发

### 使用 concurrent.futures

```python
from concurrent.futures import ThreadPoolExecutor

def fetch_data(url):
    # 网络请求
    pass

with ThreadPoolExecutor(max_workers=5) as executor:
    futures = [executor.submit(fetch_data, url) for url in urls]
    results = [f.result() for f in futures]
```

### 避免全局解释器锁（GIL）

- I/O 密集：用线程
- CPU 密集：用多进程（multiprocessing）

---

## 性能优化

### 使用内置函数

```python
# ✓ 快
sum(numbers)

# ✗ 慢
total = 0
for n in numbers:
    total += n
```

### 避免循环中字符串拼接

```python
# ✓ 快
parts = []
for item in items:
    parts.append(str(item))
result = ''.join(parts)

# ✗ 慢
result = ''
for item in items:
    result += str(item)
```

---

## 反模式（避免）

### 不要用可变默认参数

```python
# ✗ 错误
def append_item(item, items=[]):
    items.append(item)
    return items

# ✓ 正确
def append_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items
```

### 不要用 == 比较 None

```python
# ✗ 错误
if user == None:
    pass

# ✓ 正确
if user is None:
    pass
```

### 不要在循环中 import

```python
# ✗ 错误
for item in items:
    import json
    data = json.loads(item)

# ✓ 正确
import json
for item in items:
    data = json.loads(item)
```

---

## AI 编码检查清单

- [ ] 遵循 PEP 8
- [ ] 使用 snake_case 命名变量/函数
- [ ] 使用 PascalCase 命名类
- [ ] 使用类型提示
- [ ] 使用 with 语句管理资源
- [ ] 使用生成器处理大数据
- [ ] 避免可变默认参数
- [ ] 使用 `is` 而非 `==` 比较 None
- [ ] 使用具体异常而非 broad `except:`
