# Vibe Coding - AI 编程约束系统

> 给 AI Agent 提供编程约束指导，确保代码风格可控、架构清晰。

## 项目目标

将编程最佳实践、架构原则、风格规范集中管理，让 AI 在编码前先读取本项目，确保输出符合高标准。

---

## 快速开始

### 1. 克隆项目

```bash
git clone https://github.com/leozhang2056/vibe-coding.git
cd vibe-coding
```

### 2. 让 AI 读取

开始新项目前：

```
请先阅读 /path/to/vibe-coding/ 项目，理解编程约束和风格指南。
```

---

## 文档结构

```
vibe-coding/
├── common/                    # 通用原则（语言无关）
│   ├── principles/           # 核心原则
│   │   ├── SOLID.md               # 单一职责、开闭原则等
│   │   ├── CLEAN_CODE.md          # 代码整洁之道
│   │   ├── CLEAN_ARCHITECTURE.md  # 简洁架构
│   │   └── DRY_KISS_YAGNI.md      # DRY、KISS、YAGNI 原则
│   ├── patterns/             # 设计模式
│   │   └── DESIGN_PATTERNS.md     # 工厂、策略、观察者等
│   └── anti-patterns/        # 反模式
│       └── ANTI_PATTERNS.md       # 魔法数字、嵌套地狱等
│
├── languages/                 # 语言特定规范
│   ├── python/               # Python 规范
│   │   └── STYLE.md              # PEP 8、Pythonic 写法
│   ├── javascript/           # JavaScript 规范
│   │   └── STYLE.md              # ES6+、Airbnb 风格
│   ├── go/                   # Go 规范（即将添加）
│   └── rust/                 # Rust 规范（即将添加）
│
└── templates/                # 项目模板（即将添加）
```

---

## 核心原则

### SOLID 原则

- **S**ingle Responsibility - 单一职责
- **O**pen/Closed - 开闭原则
- **L**iskov Substitution - 里氏替换
- **I**nterface Segregation - 接口隔离
- **D**ependency Inversion - 依赖倒置

### Clean Code

- 有意义的命名
- 函数短小，只做一件事
- 避免重复 (DRY)
- 保持简单 (KISS)
- 代码即文档

### Clean Architecture

- 依赖规则：外层 → 内层
- 框架无关
- 可测试
- UI 无关

### 设计模式

- 创建型：工厂、建造者、单例
- 结构型：适配器、装饰器
- 行为型：策略、观察者、命令

---

## 使用场景

### 场景 1：项目初始化

```
你：我要开发一个 Python Web 应用，请先阅读 vibe-coding 项目，
了解编码规范和架构原则，然后根据这些约束设计项目结构。
```

### 场景 2：编写代码

```
你：编写一个用户注册功能，按照 vibe-coding 中的 Python 规范，
确保符合 SOLID 原则。
```

### 场景 3：代码审查

```
你：根据 vibe-coding 的 Clean Code 标准审查以下代码，
指出不符合规范的地方。
```

---

## 语言支持

| 语言 | 状态 | 文档 |
|------|------|------|
| Python | ✅ 完成 | `languages/python/STYLE.md` |
| JavaScript | ✅ 完成 | `languages/javascript/STYLE.md` |
| Go | 🚧 计划中 | - |
| Rust | 🚧 计划中 | - |

---

## 参考

基于以下经典著作：

- **Clean Code** - Robert C. Martin
- **Clean Architecture** - Robert C. Martin
- **The Pragmatic Programmer** - David Thomas
- **Design Patterns** - GoF

---

## 贡献

欢迎补充：

- 更多语言规范
- 设计模式示例
- 最佳实践案例
- 反模式案例

---

## License

MIT

---

## GitHub

https://github.com/leozhang2056/vibe-coding
