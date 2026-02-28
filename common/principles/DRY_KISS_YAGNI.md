# DRY & KISS 原则

> Don't Repeat Yourself & Keep It Simple, Stupid

## DRY - Don't Repeat Yourself

> 每一知识都必须在系统内单一、无歧义地表达。

### 原则

- 不要重复代码
- 提取公共逻辑
- 复用而非复制

### 示例

```python
# ❌ 违反 DRY
def process_user(user):
    if user.age >= 18:
        print("成年")
        # ... 成人逻辑
    else:
        print("未成年")
        # ... 未成年逻辑

def process_admin(admin):
    if admin.age >= 18:
        print("成年")
        # ... 成人逻辑
    else:
        print("未成年")
        # ... 未成年逻辑

# ✅ 符合 DRY
def is_adult(person):
    return person.age >= 18

def process_person(person):
    if is_adult(person):
        print("成年")
        # ... 成人逻辑
    else:
        print("未成年")
        # ... 未成年逻辑
```

---

## KISS - Keep It Simple, Stupid

> 简单是复杂性的终极 sophistication。

### 原则

- 保持代码简单
- 避免过度设计
- 优先选择简单方案

### 示例

```python
# ❌ 过度复杂
class UserValidatorFactory:
    def create_validator(self, user_type):
        if user_type == "admin":
            return AdminValidator()
        elif user_type == "regular":
            return RegularValidator()

# ✅ 简单直接
def validate_user(user, user_type):
    if user_type == "admin":
        return validate_admin(user)
    return validate_regular(user)
```

---

## YAGNI - You Aren't Gonna Need It

> 不要实现你当前不需要的功能。

### 原则

- 只实现当前需要的功能
- 不要为未来过度设计
- 需要时再加

### 示例

```python
# ❌ 过度设计
class UserService:
    def __init__(self):
        self.cache = LRUCache()  # 还不需要缓存
        self.metrics = Metrics()  # 还不需要指标
        self.events = EventBus()  # 还不需要事件
        self.audit_log = AuditLog()  # 还不需要审计

# ✅ 简单实现
class UserService:
    def __init__(self):
        self.db = Database()
```

---

## AI 编码检查清单

- [ ] 没有重复代码
- [ ] 提取了公共逻辑
- [ ] 选择简单方案而非复杂方案
- [ ] 没有实现不需要的功能
- [ ] 代码易于理解
