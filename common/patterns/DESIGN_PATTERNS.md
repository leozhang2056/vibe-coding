# 设计模式

> 可复用的面向对象软件解决方案

## 创建型模式

### 工厂模式（Factory）

> 定义创建对象的接口，让子类决定实例化哪个类。

```python
from abc import ABC, abstractmethod

class PaymentProcessor(ABC):
    @abstractmethod
    def process(self, amount):
        pass

class CreditCardProcessor(PaymentProcessor):
    def process(self, amount):
        return f"信用卡支付 ${amount}"

class PayPalProcessor(PaymentProcessor):
    def process(self, amount):
        return f"PayPal 支付 ${amount}"

class PaymentFactory:
    @staticmethod
    def create_processor(payment_type):
        if payment_type == "credit_card":
            return CreditCardProcessor()
        elif payment_type == "paypal":
            return PayPalProcessor()
        else:
            raise ValueError(f"不支持的支付方式: {payment_type}")

# 使用
processor = PaymentFactory.create_processor("credit_card")
processor.process(100)
```

---

### 建造者模式（Builder）

> 分步骤创建复杂对象。

```python
class User:
    def __init__(self):
        self.name = None
        self.email = None
        self.age = None

class UserBuilder:
    def __init__(self):
        self.user = User()
    
    def with_name(self, name):
        self.user.name = name
        return self
    
    def with_email(self, email):
        self.user.email = email
        return self
    
    def with_age(self, age):
        self.user.age = age
        return self
    
    def build(self):
        return self.user

# 使用
user = (UserBuilder()
    .with_name("John")
    .with_email("john@example.com")
    .with_age(30)
    .build())
```

---

### 单例模式（Singleton）

> 确保一个类只有一个实例。

```python
class Database:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
            cls._instance.connection = None
        return cls._instance

# 使用
db1 = Database()
db2 = Database()
assert db1 is db2  # True
```

---

## 结构型模式

### 适配器模式（Adapter）

> 让不兼容的接口协同工作。

```python
class EmailService:
    def send_email(self, to, subject, body):
        print(f"Email to {to}: {subject}")

class SMSAdapter:
    """将 SMS 服务适配成邮件接口"""
    def __init__(self, sms_service):
        self.sms = sms_service
    
    def send_email(self, to, subject, body):
        # 将邮件内容转换为短信
        self.sms.send_sms(to, f"{subject}: {body[:160]}")
```

---

### 装饰器模式（Decorator）

> 动态地给对象添加职责。

```python
from functools import wraps

def timer(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        print(f"{func.__name__} took {time.time() - start}s")
        return result
    return wrapper

def cache(func):
    _cache = {}
    @wraps(func)
    def wrapper(*args):
        if args not in _cache:
            _cache[args] = func(*args)
        return _cache[args]
    return wrapper

@timer
@cache
def expensive_function(n):
    return n ** 2
```

---

## 行为型模式

### 策略模式（Strategy）

> 定义算法族，分别封装，让它们可互换。

```python
from abc import ABC, abstractmethod

class DiscountStrategy(ABC):
    @abstractmethod
    def apply(self, price):
        pass

class NoDiscount(DiscountStrategy):
    def apply(self, price):
        return price

class TenPercentOff(DiscountStrategy):
    def apply(self, price):
        return price * 0.9

class HalfPrice(DiscountStrategy):
    def apply(self, price):
        return price * 0.5

class ShoppingCart:
    def __init__(self, discount_strategy):
        self.discount_strategy = discount_strategy
    
    def calculate_total(self, price):
        return self.discount_strategy.apply(price)

# 使用
cart = ShoppingCart(TenPercentOff())
total = cart.calculate_total(100)  # 90
```

---

### 观察者模式（Observer）

> 一对多依赖，当对象改变时，依赖者收到通知。

```python
class Subject:
    def __init__(self):
        self._observers = []
    
    def attach(self, observer):
        self._observers.append(observer)
    
    def detach(self, observer):
        self._observers.remove(observer)
    
    def notify(self, data):
        for observer in self._observers:
            observer.update(data)

class User(Subject):
    def __init__(self, name):
        super().__init__()
        self.name = name
    
    def post_message(self, message):
        self.notify({
            "user": self.name,
            "message": message
        })

class Follower:
    def update(self, data):
        print(f"收到 {data['user']} 的消息: {data['message']}")

# 使用
user = User("Alice")
follower1 = Follower()
follower2 = Follower()

user.attach(follower1)
user.attach(follower2)

user.post_message("Hello!")  # 两个 follower 都会收到
```

---

### 命令模式（Command）

> 将请求封装成对象。

```python
from abc import ABC, abstractmethod

class Command(ABC):
    @abstractmethod
    def execute(self):
        pass
    
    @abstractmethod
    def undo(self):
        pass

class Light:
    def turn_on(self):
        print("灯已打开")
    
    def turn_off(self):
        print("灯已关闭")

class LightOnCommand(Command):
    def __init__(self, light):
        self.light = light
    
    def execute(self):
        self.light.turn_on()
    
    def undo(self):
        self.light.turn_off()

class RemoteControl:
    def __init__(self):
        self.history = []
    
    def press(self, command):
        command.execute()
        self.history.append(command)
    
    def undo(self):
        if self.history:
            command = self.history.pop()
            command.undo()

# 使用
remote = RemoteControl()
light = Light()
on_command = LightOnCommand(light)

remote.press(on_command)
remote.undo()
```

---

## 反模式（避免）

### 1. God Object（上帝对象）

> 一个类做太多事情

```python
# ❌ 反模式
class Application:
    def handle_user(self):
        pass
    
    def send_email(self):
        pass
    
    def save_to_db(self):
        pass
    
    def render_ui(self):
        pass
```

### 2. Singleton 依赖

> 过度使用单例导致全局状态

```python
# ❌ 反模式
def process_data():
    db = Database.instance()  # 隐藏依赖
    db.save(data)
```

---

## AI 编码检查清单

- [ ] 识别何时使用设计模式
- [ ] 优先组合而非继承
- [ ] 避免过度设计
- [ ] 模式名称能清晰表达意图
