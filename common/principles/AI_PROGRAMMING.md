# AI 编程最佳实践

> 与 AI 协作编程的核心原则

## 核心理念

Vibe Coding 的本质是：**让 AI 成为你的编程伙伴，而不是替代品**。

---

## 1. 提示词工程（Prompt Engineering）

### 明确的指令

```python
# ❌ 模糊的提示
"写一个用户登录功能"

# ✅ 清晰的提示
"用 Python Flask 写一个用户登录功能，要求：
1. 使用 JWT 认证
2. 密码使用 bcrypt 加密
3. 返回标准的 JSON 响应
4. 包含输入验证
5. 遵循 SOLID 原则"
```

### 提供上下文

```
# ✅ 好的提示（提供背景）
"我正在开发一个电商网站的后端 API，使用 Python Flask。
请帮我写一个订单创建接口，需要调用支付网关、发送邮件通知、
记录订单日志。请遵循 Clean Architecture 原则。"
```

---

## 2. 渐进式开发

### 从小处着手

```
1. 先实现核心功能
2. 逐步添加辅助功能
3. 最后优化和完善
```

### 示例

```
第 1 步：实现基本的 CRUD
第 2 步：添加数据验证
第 3 步：添加缓存
第 4 步：添加日志
第 5 步：添加测试
```

---

## 3. 代码审查循环

### 人机协作流程

```
1. 让 AI 写第一版代码
2. 人工审查关键逻辑
3. 指出问题，让 AI 修改
4. 重复直到满意
5. 人工测试和验证
```

---

## 4. 质量保障

### AI 辅助测试

```python
# 让 AI 生成测试用例
"请为以下函数编写单元测试，使用 pytest，
覆盖正常情况、边界情况、异常情况"

def calculate_discount(price, discount_rate):
    return price * (1 - discount_rate)
```

---

## 5. 文档化

### 代码注释

```python
# ❌ 让 AI 写很多注释
"给每个函数都加上详细注释"

# ✅ 让代码自解释
"写出清晰易懂的代码，变量名和函数名要能表达意图，
尽量减少注释的使用"
```

---

## 6. 安全意识

### 常见安全问题

```python
# ❌ 不安全的代码（SQL 注入）
query = f"SELECT * FROM users WHERE name = '{name}'"

# ✅ 安全的代码
query = "SELECT * FROM users WHERE name = ?"
cursor.execute(query, (name,))
```

---

## 7. 版本控制

### Git 工作流

```bash
# 开发新功能
git checkout -b feature/add-user-auth

# 完成后提交
git add .
git commit -m "feat: 添加用户认证功能"
git push origin feature/add-user-auth
```

---

## 8. 性能优化

### 性能原则

```python
# ❌ 过早优化
# 在数据量小时就加缓存

# ✅ 先简单实现，有需要再优化
def process_data(items):
    return [process(item) for item in items]
```

---

## AI 编码检查清单

- [ ] 提示词清晰明确
- [ ] 提供足够的上下文
- [ ] 从简单功能开始，逐步扩展
- [ ] 人工审查关键代码
- [ ] 编写单元测试
- [ ] 遵循安全最佳实践
- [ ] 使用版本控制
- [ ] 代码自解释，减少注释
