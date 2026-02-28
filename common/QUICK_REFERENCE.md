# 快速参考指南

> 常用编程模式和最佳实践速查

## 命名规范

| 语言 | 变量 | 函数 | 类 | 常量 |
|------|------|------|------|------|
| Python | snake_case | snake_case | PascalCase | UPPER_SNAKE_CASE |
| JavaScript | camelCase | camelCase | PascalCase | UPPER_SNAKE_CASE |
| Go | camelCase | PascalCase | PascalCase | PascalCase 或 camelCase |
| Java | camelCase | camelCase | PascalCase | UPPER_SNAKE_CASE |

---

## 常见设计模式

### 创建型

- **工厂** (`factory`) - 创建对象接口
- **建造者** (`builder`) - 分步创建
- **单例** (`singleton`) - 唯一实例

### 结构型

- **适配器** (`adapter`) - 接口转换
- **装饰器** (`decorator`) - 动态添加职责
- **外观** (`facade`) - 简化接口

### 行为型

- **策略** (`strategy`) - 算法可互换
- **观察者** (`observer`) - 事件订阅
- **命令** (`command`) - 请求封装

---

## 错误处理

### Python

```python
# ✓ 正确
try:
    result = risky_operation()
except SpecificError as e:
    logger.error(f"操作失败: {e}")
    raise
```

### JavaScript

```javascript
// ✓ 正确
try {
  const result = await riskyOperation();
} catch (error) {
  console.error('操作失败:', error);
  throw error;
}
```

---

## 数据验证

### Python

```python
from pydantic import BaseModel, EmailStr, Field

class UserCreate(BaseModel):
    name: str = Field(..., min_length=1, max_length=50)
    email: EmailStr
    age: int = Field(..., ge=0, le=150)
```

### JavaScript

```javascript
// 使用 Joi 或 Zod
const schema = z.object({
  name: z.string().min(1).max(50),
  email: z.string().email(),
  age: z.number().min(0).max(150)
});
```

---

## 日志记录

### Python

```python
from loguru import logger

logger.info("用户登录", extra={"user_id": user.id})
logger.error("数据库错误", extra={"error": str(e)})
```

### JavaScript

```javascript
console.info('用户登录', { userId: user.id });
console.error('数据库错误', { error: e.message });
```

---

## 配置管理

### Python

```python
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    database_url: str
    secret_key: str
    debug: bool = False
    
    class Config:
        env_file = ".env"
```

### JavaScript

```javascript
// 使用 dotenv
require('dotenv').config();

export const config = {
  databaseUrl: process.env.DATABASE_URL,
  secretKey: process.env.SECRET_KEY,
  debug: process.env.DEBUG === 'true'
};
```

---

## 依赖注入

### Python

```python
from functools import lru_cache
from typing import Protocol

class Database(Protocol):
    def save(self, data):
        ...

class UserService:
    def __init__(self, db: Database):
        self.db = db
```

### JavaScript

```javascript
class UserService {
  constructor(db) {
    this.db = db;
  }
}
```

---

## API 设计

### RESTful

```
GET    /users        # 列表
GET    /users/:id    # 详情
POST   /users        # 创建
PUT    /users/:id    # 更新
DELETE /users/:id    # 删除
```

### 响应格式

```json
{
  "data": {...},
  "error": null,
  "message": "Success"
}
```

---

## 数据库操作

### 避免问题

```python
# ✗ N+1 查询
for user in users:
    orders = get_orders(user.id)

# ✓ 一次查询
users_orders = get_users_with_orders()
```

---

## 测试

### 测试金字塔

```
    /\
   /  \        E2E Tests (少量)
  /    \
 /      \
/________\
Unit Tests (大量) + Integration Tests (适量)
```

---

## 性能优化

### 优先级

1. 算法优化（O(n²) → O(n log n)）
2. 数据库查询优化
3. 缓存
4. 异步处理

---

## 安全检查清单

- [ ] 输入验证
- [ ] SQL 注入防护
- [ ] XSS 防护
- [ ] CSRF 防护
- [ ] 敏感数据加密
- [ ] HTTPS
- [ ] 最小权限原则
