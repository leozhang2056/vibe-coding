# Clean Architecture - 简洁架构

> 基于 Robert C. Martin 的《Clean Architecture》

## 核心规则

### 依赖规则

> 源代码依赖只能指向内层，永不指向外层。

```
┌─────────────────────────────────┐
│      Frameworks & Drivers       │  ← 最外层
│  (Web, DB, Frameworks, UI)       │
├─────────────────────────────────┤
│           Interface Adapters     │
│    (Controllers, Presenters)     │
├─────────────────────────────────┤
│            Use Cases             │
│      (Business Rules)            │
├─────────────────────────────────┤
│           Entities               │  ← 最内层
│     (Enterprise Business Rules)  │
└─────────────────────────────────┘
```

### 层次说明

| 层 | 职责 | 依赖 |
|----|------|------|
| **Entities** | 核心业务规则 | 无 |
| **Use Cases** | 应用特定业务规则 | 依赖 Entities |
| **Interface Adapters** | 数据格式转换 | 依赖 Use Cases |
| **Frameworks** | 框架、工具 | 依赖所有内层 |

---

## Entities (实体层)

### 定义

- 企业的核心业务规则
- 与框架无关
- 可用于任何应用

### 示例

```python
# core/entities/user.py
class User:
    """用户实体 - 核心业务规则"""
    
    def __init__(self, email: str):
        if not self._is_valid_email(email):
            raise ValueError("邮箱格式无效")
        self.email = email
    
    def _is_valid_email(self, email: str) -> bool:
        return "@" in email
```

---

## Use Cases (用例层)

### 定义

- 应用特定的业务规则
- 编排 Entities 完成操作
- 不知道数据库、Web 等细节

### 示例

```python
# core/use_cases/register_user.py
class RegisterUser:
    """注册用户用例"""
    
    def __init__(self, user_repository, email_service):
        self.user_repository = user_repository
        self.email_service = email_service
    
    def execute(self, email: str, password: str):
        # 1. 创建用户实体
        user = User(email)
        
        # 2. 保存到仓储
        self.user_repository.save(user)
        
        # 3. 发送欢迎邮件
        self.email_service.send_welcome(user)
```

---

## Interface Adapters (接口适配器层)

### Controllers (控制器)

```python
# adapters/controllers/user_controller.py
class UserController:
    """HTTP 控制器"""
    
    def __init__(self, register_user_use_case):
        self.register_user = register_user_use_case
    
    def register(self, request):
        # 将 HTTP 请求转换为用例参数
        email = request.json["email"]
        password = request.json["password"]
        
        # 调用用例
        self.register_user.execute(email, password)
        
        # 返回 HTTP 响应
        return {"status": "success"}
```

### Presenters (展示器)

```python
# adapters/presenters/user_presenter.py
class UserPresenter:
    """用户数据展示器"""
    
    def present(self, user: User):
        # 将实体转换为 JSON
        return {
            "email": user.email,
            "created_at": user.created_at.isoformat()
        }
```

### Repositories (仓储接口)

```python
# core/repositories/user_repository.py
from abc import ABC, abstractmethod

class UserRepository(ABC):
    """用户仓储接口（定义在核心层）"""
    
    @abstractmethod
    def save(self, user: User):
        pass
    
    @abstractmethod
    def find_by_email(self, email: str) -> User:
        pass
```

### Repository 实现（在框架层）

```python
# frameworks/database/sql_user_repository.py
class SQLUserRepository(UserRepository):
    """SQL 实现（在框架层）"""
    
    def __init__(self, db_connection):
        self.db = db_connection
    
    def save(self, user: User):
        self.db.execute(
            "INSERT INTO users (email) VALUES (?)",
            (user.email,)
        )
    
    def find_by_email(self, email: str) -> User:
        row = self.db.query(
            "SELECT * FROM users WHERE email = ?",
            (email,)
        )
        return User(row["email"])
```

---

## Frameworks (框架层)

### Web 框架集成

```python
# frameworks/flask/routes.py
from flask import Blueprint, request

user_bp = Blueprint('users', __name__)

@user_bp.route('/users', methods=['POST'])
def register():
    # 使用控制器
    return user_controller.register(request)
```

### 数据库集成

```python
# frameworks/database/connection.py
import sqlite3

def get_connection():
    return sqlite3.connect('app.db')
```

---

## 项目结构示例

```
my_project/
├── core/                    # 核心层
│   ├── entities/           # 实体
│   │   └── user.py
│   ├── use_cases/          # 用例
│   │   └── register_user.py
│   └── repositories/       # 仓储接口
│       └── user_repository.py
│
├── adapters/               # 适配器层
│   ├── controllers/        # 控制器
│   │   └── user_controller.py
│   ├── presenters/         # 展示器
│   │   └── user_presenter.py
│   └── repositories/       # 仓储实现
│       └── sql_user_repository.py
│
└── frameworks/             # 框架层
    ├── flask/             # Flask 集成
    │   └── routes.py
    └── database/          # 数据库
        └── connection.py
```

---

## 依赖注入

### 原则

- 依赖通过构造函数注入
- 不要在函数内部 `import` 或实例化依赖

### 示例

```python
# ❌ 错误：硬编码依赖
class UserController:
    def __init__(self):
        self.db = SQLiteConnection()  # 硬编码

# ✅ 正确：注入依赖
class UserController:
    def __init__(self, register_user_use_case):
        self.register_user = register_user_use_case

# 组装根
def main():
    # 创建最外层依赖
    db_connection = SQLiteConnection()
    user_repository = SQLUserRepository(db_connection)
    email_service = SMTPEmailService()
    
    # 创建用例
    register_user = RegisterUser(user_repository, email_service)
    
    # 创建控制器
    user_controller = UserController(register_user)
    
    # 启动应用
    app.run()
```

---

## 测试

### 单元测试用例

```python
def test_register_user_use_case():
    # 使用 Mock 仓储
    mock_repo = MockUserRepository()
    mock_email = MockEmailService()
    
    use_case = RegisterUser(mock_repo, mock_email)
    use_case.execute("test@example.com", "password")
    
    # 验证交互
    assert mock_repo.was_saved
    assert mock_email.was_sent
```

---

## AI 编码检查清单

编码时，确保架构符合以下检查：

- [ ] 依赖方向正确（外层 → 内层）
- [ ] Entities 不依赖任何框架
- [ ] Use Cases 不依赖 Web/DB
- [ ] 所有依赖通过构造函数注入
- [ ] 接口定义在核心层
- [ ] 实现在外层
- [ ] 可独立测试每层
