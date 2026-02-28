# JavaScript 编码规范

> 现代 JavaScript (ES6+) 最佳实践

## 代码风格

### 使用 Airbnb 风格指南

```javascript
// ✓ 正确
const getUser = (userId) => {
  return fetch(`/api/users/${userId}`)
    .then(response => response.json());
};

// ✗ 错误
function getUser(userId) {
  return fetch('/api/users/' + userId)
    .then(function(response) {
      return response.json();
    });
}
```

### 命名规范

| 类型 | 规范 | 示例 |
|------|------|------|
| 变量/常量 | camelCase | `userName`, `isLoggedIn` |
| 类/构造函数 | PascalCase | `UserController`, `UserService` |
| 常量（不可变） | UPPER_SNAKE_CASE | `MAX_RETRIES`, `API_BASE_URL` |
| 私有成员 | 前缀下划线 | `_internalMethod`, `privateVar` |

### 使用 const 和 let

```javascript
// ✓ 正确
const PI = 3.14;
let count = 0;
count++;

// ✗ 错误（不要用 var）
var name = 'John';
```

---

## 现代 ES6+ 特性

### 箭头函数

```javascript
// ✓ 箭头函数
const add = (a, b) => a + b;

// ✓ 多行箭头函数
const fetchUser = async (userId) => {
  const response = await fetch(`/api/users/${userId}`);
  return response.json();
};

// ✗ 避免匿名函数
setTimeout(function() {
  console.log('Hello');
}, 1000);
```

### 模板字符串

```javascript
// ✓ 正确
const message = `Hello, ${userName}!`;

// ✗ 错误
const message = 'Hello, ' + userName + '!';
```

### 解构赋值

```javascript
// ✓ 对象解构
const { name, email } = user;

// ✓ 数组解构
const [first, second] = array;

// ✓ 函数参数解构
const createUser = ({ name, email }) => {
  // ...
};
```

### 展开运算符

```javascript
// ✓ 数组展开
const newArray = [...oldArray, newItem];

// ✓ 对象展开
const newUser = { ...user, age: 30 };

// ✓ 函数参数展开
const sum = (...numbers) => numbers.reduce((a, b) => a + b, 0);
```

---

## 异步处理

### 优先使用 async/await

```javascript
// ✓ 正确
const fetchUser = async (userId) => {
  try {
    const response = await fetch(`/api/users/${userId}`);
    const user = await response.json();
    return user;
  } catch (error) {
    console.error('Fetch error:', error);
    throw error;
  }
};

// ✗ 避免回调地狱
fetchUser(userId, (user) => {
  updateUser(user, (success) => {
    if (success) {
      console.log('Done');
    }
  });
});
```

### Promise 链式调用

```javascript
// ✓ 正确
fetchUser(userId)
  .then(user => updateUser(user))
  .then(success => console.log('Done'))
  .catch(error => console.error('Error:', error));

// ✗ 错误（忘记 catch）
fetchUser(userId)
  .then(user => updateUser(user));
```

---

## 错误处理

### 具体错误类型

```javascript
// ✓ 正确
try {
  const user = await getUser(userId);
} catch (error) {
  if (error instanceof NetworkError) {
    console.error('Network error:', error.message);
  } else if (error instanceof ValidationError) {
    console.error('Validation error:', error.errors);
  } else {
    console.error('Unknown error:', error);
  }
}
```

### 抛出有意义的错误

```javascript
// ✓ 正确
function validateUser(user) {
  if (!user.email) {
    throw new ValidationError('Email is required');
  }
  if (!user.email.includes('@')) {
    throw new ValidationError('Invalid email format');
  }
}
```

---

## 对象和数组

### 使用简写

```javascript
// ✓ 正确
const name = 'John';
const age = 30;
const user = { name, age };

// ✓ 方法简写
const user = {
  name,
  greet() {
    console.log(`Hello, I'm ${this.name}`);
  }
};
```

### 不可变性

```javascript
// ✓ 正确（不修改原对象）
const updatedUser = { ...user, age: 31 };
const newArray = array.filter(item => item.id !== idToRemove);

// ✗ 错误（修改原对象）
user.age = 31;
```

### 数组方法

```javascript
// ✓ map（转换）
const doubled = numbers.map(n => n * 2);

// ✓ filter（过滤）
const adults = users.filter(u => u.age >= 18);

// ✓ reduce（累加）
const sum = numbers.reduce((acc, n) => acc + n, 0);

// ✓ find（查找）
const user = users.find(u => u.id === userId);

// ✓ some/every（检查）
const hasAdult = users.some(u => u.age >= 18);
const allAdults = users.every(u => u.age >= 18);
```

---

## 函数

### 纯函数

```javascript
// ✓ 纯函数（无副作用）
const add = (a, b) => a + b;

// ✗ 非纯函数（有副作用）
let count = 0;
const increment = () => {
  count += 1;
  return count;
};
```

### 函数组合

```javascript
// ✓ 正确
const pipe = (...fns) => (x) => fns.reduce((v, f) => f(v), x);

const addOne = x => x + 1;
const double = x => x * 2;

const addOneAndDouble = pipe(addOne, double);
console.log(addOneAndDouble(5)); // 12
```

---

## 模块系统

### 使用 ES Modules

```javascript
// ✓ 导出（named export）
export const API_URL = 'https://api.example.com';
export const fetchUser = async (id) => {
  // ...
};

// ✓ 导出（default export）
export default class UserService {
  // ...
}

// ✓ 导入
import { fetchUser, API_URL } from './user.js';
import UserService from './UserService.js';
```

### 避免命名空间污染

```javascript
// ✗ 错误（全局变量）
window.API_URL = 'https://api.example.com';

// ✓ 正确（模块作用域）
export const API_URL = 'https://api.example.com';
```

---

## 性能优化

### 避免不必要的重新渲染

```javascript
// ✓ 使用 useMemo
const expensiveValue = useMemo(() => {
  return computeExpensiveValue(data);
}, [data]);

// ✓ 使用 useCallback
const handleClick = useCallback(() => {
  doSomething(dependency);
}, [dependency]);
```

### 防抖和节流

```javascript
// ✓ 防抖（Debounce）
const debouncedSearch = debounce(search, 300);

// ✓ 节流（Throttle）
const throttledScroll = throttle(handleScroll, 100);
```

---

## 测试

### 使用 Jest

```javascript
// ✓ 正确
describe('UserService', () => {
  it('should fetch user by ID', async () => {
    const user = await userService.getUser(1);
    expect(user.name).toBe('John');
  });

  it('should throw error for invalid ID', async () => {
    await expect(userService.getUser(-1))
      .rejects.toThrow('Invalid user ID');
  });
});
```

---

## 反模式（避免）

### 不要用 == 比较

```javascript
// ✗ 错误
if (user == null) {
  // ...
}

// ✓ 正确
if (user === null) {
  // ...
}
```

### 不要在循环中创建函数

```javascript
// ✗ 错误
buttons.forEach((button) => {
  button.onClick = () => {
    console.log('Clicked');
  };
});

// ✓ 正确
const handleClick = () => {
  console.log('Clicked');
};
buttons.forEach((button) => {
  button.onClick = handleClick;
});
```

### 不要嵌套三元运算符

```javascript
// ✗ 错误
const result = condition1
  ? value1
  : condition2
    ? value2
    : value3;

// ✓ 正确（使用 if/else）
let result;
if (condition1) {
  result = value1;
} else if (condition2) {
  result = value2;
} else {
  result = value3;
}
```

---

## AI 编码检查清单

- [ ] 使用 const/let（不用 var）
- [ ] 使用箭头函数
- [ ] 使用 async/await（不用回调）
- [ ] 使用模板字符串
- [ ] 使用解构赋值
- [ ] 使用展开运算符
- [ ] 使用 === 而非 ==
- [ ] 避免全局变量
- [ ] 使用 ES Modules（import/export）
- [ ] 编写单元测试
