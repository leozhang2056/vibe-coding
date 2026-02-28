# SOLID 原则

> 单一职责、开闭原则、里氏替换、接口隔离、依赖倒置

## S - Single Responsibility Principle (单一职责原则)

> 一个类应该只有一个引起它变化的原因。

### 定义

- 一个类只负责一件事
- 一个类只有一个改变的理由

### 示例

**违反 SRP：**

```python
class User:
    def save(self):
        # 保存到数据库
        pass
    
    def send_email(self, message):
        # 发送邮件
        pass
```

**符合 SRP：**

```python
class User:
    def save(self):
        pass

class EmailService:
    def send(self, user, message):
        pass
```

### AI 约束

> 编写类时，确保每个类只有一个职责。如果一个类有多个改变的理由，拆分它。

---

## O - Open/Closed Principle (开闭原则)

> 软件实体应该对扩展开放，对修改关闭。

### 定义

- 通过扩展来增加新功能
- 不修改现有代码

### 示例

**违反 OCP：**

```python
def calculate_discount(customer_type):
    if customer_type == "VIP":
        return 0.2
    elif customer_type == "Regular":
        return 0.1
    # 每次增加新客户类型都要修改这里
```

**符合 OCP：**

```python
class DiscountStrategy:
    def get_discount(self):
        pass

class VIPDiscount(DiscountStrategy):
    def get_discount(self):
        return 0.2

class RegularDiscount(DiscountStrategy):
    def get_discount(self):
        return 0.1
```

### AI 约束

> 添加新功能时，优先通过扩展（继承、接口）实现，而不是修改现有代码。

---

## L - Liskov Substitution Principle (里氏替换原则)

> 子类必须能够替换父类，而不破坏程序正确性。

### 定义

- 如果 S 是 T 的子类型，那么 T 对象可以替换为 S 对象
- 子类不能违反父类的契约

### 示例

**违反 LSP：**

```python
class Bird:
    def fly(self):
        pass

class Penguin(Bird):
    def fly(self):
        raise NotImplementedError("Penguins can't fly")
```

**符合 LSP：**

```python
class Bird:
    pass

class FlyingBird(Bird):
    def fly(self):
        pass

class Penguin(Bird):
    def swim(self):
        pass
```

### AI 约束

> 确保子类可以安全替换父类。如果子类不能完全实现父类行为，重新设计继承关系。

---

## I - Interface Segregation Principle (接口隔离原则)

> 客户端不应该依赖它不需要的接口。

### 定义

- 接口应该小而专注
- 客户端只依赖需要的接口

### 示例

**违反 ISP：**

```python
class Worker(Protocol):
    def work(self):
        pass
    
    def eat(self):
        pass

class Robot(Worker):
    def work(self):
        pass
    
    def eat(self):
        pass  # 机器人不需要吃饭
```

**符合 ISP：**

```python
class Workable(Protocol):
    def work(self):
        pass

class Eatable(Protocol):
    def eat(self):
        pass

class Robot(Workable):
    def work(self):
        pass
```

### AI 约束

> 设计接口时，保持接口最小化。不要强迫客户端实现不需要的方法。

---

## D - Dependency Inversion Principle (依赖倒置原则)

> 高层模块不应该依赖低层模块，两者都应该依赖抽象。

### 定义

- 依赖抽象（接口），不依赖具体实现
- 高层和低层都依赖接口

### 示例

**违反 DIP：**

```python
class LightBulb:
    def turn_on(self):
        pass

class Switch:
    def __init__(self):
        self.bulb = LightBulb()  # 依赖具体实现
```

**符合 DIP：**

```python
class Switchable(Protocol):
    def turn_on(self):
        pass

class LightBulb(Switchable):
    def turn_on(self):
        pass

class Switch:
    def __init__(self, device: Switchable):
        self.device = device  # 依赖抽象
```

### AI 约束

> 依赖注入时，通过接口/协议注入依赖，而不是直接实例化具体类。

---

## AI 编码检查清单

编码时，确保代码符合以下检查：

- [ ] 每个类只有一个职责
- [ ] 新功能通过扩展而非修改实现
- [ ] 子类可以完全替换父类
- [ ] 接口保持最小化
- [ ] 依赖抽象而非具体实现
