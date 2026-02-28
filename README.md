# Vibe Coding - AI 编程约束系统

> 给 AI Agent 提供编程约束指导，确保代码风格可控、架构清晰。

## 项目目标

将编程最佳实践、架构原则、风格规范集中管理，让 AI 在编码前先读取本项目，确保输出符合高标准。

## 核心原则

本项目基于以下经典著作：

- **Clean Code** (代码整洁之道) - Robert C. Martin
- **Clean Architecture** (简洁架构) - Robert C. Martin
- **The Pragmatic Programmer** (程序员的职业素养) - David Thomas
- **Design Patterns** (设计模式) - GoF

## 文档结构

```
vibe-coding/
├── common/                 # 通用原则（语言无关）
│   ├── principles/        # 核心原则
│   ├── patterns/          # 设计模式
│   └── anti-patterns/     # 反模式
│
├── languages/             # 语言特定规范
│   ├── python/           # Python 规范
│   ├── javascript/       # JavaScript 规范
│   ├── go/               # Go 规范
│   └── rust/             # Rust 规范
│
└── templates/            # 项目模板
```

## 如何使用

### 1. 项目初始化

在开始新项目前，让 AI 读取本项目：

```
请先阅读 /root/.openclaw/workspace/vibe-coding/ 项目，理解编程约束和风格指南。
```

### 2. 编码约束

编码时提醒 AI：

```
按照 vibe-coding 中的规范编写代码：
- 使用 Python/ 目录下的风格指南
- 遵循 common/principles/ 中的 SOLID 原则
- 避免 common/anti-patterns/ 中的反模式
```

### 3. 代码审查

代码审查时参考：

```
根据 vibe-coding 的标准审查代码：
- Clean Code 原则
- Clean Architecture 依赖规则
- 语言特定规范
```

## 核心理念

### 1. SOLID 原则

- **S**ingle Responsibility - 单一职责
- **O**pen/Closed - 开闭原则
- **L**iskov Substitution - 里氏替换
- **I**nterface Segregation - 接口隔离
- **D**ependency Inversion - 依赖倒置

### 2. Clean Code

- 有意义的命名
- 函数短小，只做一件事
- 避免重复代码 (DRY)
- 保持简单 (KISS)
- 代码即文档，减少注释

### 3. Clean Architecture

- 依赖规则：外层依赖内层
- 框架无关
- 可测试
- UI 无关

### 4. 设计模式

- 创建型：工厂、建造者、单例
- 结构型：适配器、装饰器、代理
- 行为型：策略、观察者、命令

## 贡献

欢迎补充更多语言规范、设计模式、最佳实践。

## License

MIT
