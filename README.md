# Vibe Coding - AI 编程约束系统

> 给 AI Agent 提供编程约束指导，确保代码风格可控、架构清晰。

## 项目目标

将编程最佳实践、架构原则、风格规范集中管理，让 AI 在编码前先读取本项目，确保输出符合高标准。

---

## 🚀 快速开始

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

## 📚 文档结构

```
vibe-coding/
├── common/                    # 通用原则（语言无关）
│   ├── principles/           # 核心原则
│   │   ├── SOLID.md               # 单一职责、开闭原则等
│   │   ├── CLEAN_CODE.md          # 代码整洁之道
│   │   ├── CLEAN_ARCHITECTURE.md  # 简洁架构
│   │   ├── DRY_KISS_YAGNI.md      # DRY、KISS、YAGNI 原则
│   │   └── AI_PROGRAMMING.md      # AI 编程最佳实践
│   ├── patterns/             # 设计模式
│   │   └── DESIGN_PATTERNS.md     # 工厂、策略、观察者等
│   ├── anti-patterns/        # 反模式
│   │   └── ANTI_PATTERNS.md       # 魔法数字、嵌套地狱等
│   ├── WORKFLOW.md            # Vibe Coding 工作流
│   ├── TOOLS.md               # AI 编程工具推荐
│   ├── PROJECT_STRUCTURE.md   # 项目组织结构
│   ├── GETTING_STARTED.md     # 入门指南
│   └── QUICK_REFERENCE.md     # 快速参考指南
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

## 📖 核心内容

### 编程原则

| 原则 | 说明 | 文档 |
|------|------|------|
| **SOLID** | 单一职责、开闭、里氏替换、接口隔离、依赖倒置 | [SOLID.md](common/principles/SOLID.md) |
| **Clean Code** | 代码整洁之道 | [CLEAN_CODE.md](common/principles/CLEAN_CODE.md) |
| **Clean Architecture** | 简洁架构、依赖规则 | [CLEAN_ARCHITECTURE.md](common/principles/CLEAN_ARCHITECTURE.md) |
| **DRY/KISS/YAGNI** | 不重复、保持简单、不做过度设计 | [DRY_KISS_YAGNI.md](common/principles/DRY_KISS_YAGNI.md) |
| **AI 编程** | 与 AI 协作编程的最佳实践 | [AI_PROGRAMMING.md](common/principles/AI_PROGRAMMING.md) |

### 工作流与实践

| 内容 | 说明 | 文档 |
|------|------|------|
| **Vibe Coding 工作流** | 自然语言描述 → AI 生成 → 执行测试 → 反馈修正 | [WORKFLOW.md](common/WORKFLOW.md) |
| **工具推荐** | Cursor、Copilot、Claude Code、Cline 等 | [TOOLS.md](common/TOOLS.md) |
| **项目结构** | 目录组织、Makefile、Git 提交规范 | [PROJECT_STRUCTURE.md](common/PROJECT_STRUCTURE.md) |
| **入门指南** | 从零到一，快速上手 AI 编程 | [GETTING_STARTED.md](common/GETTING_STARTED.md) |
| **设计模式** | 工厂、策略、观察者、命令等 | [DESIGN_PATTERNS.md](common/patterns/DESIGN_PATTERNS.md) |
| **反模式** | 魔法数字、嵌套地狱、过早优化等 | [ANTI_PATTERNS.md](common/anti-patterns/ANTI_PATTERNS.md) |
| **快速参考** | 命名规范、错误处理、API 设计等 | [QUICK_REFERENCE.md](common/QUICK_REFERENCE.md) |

---

## 🌐 语言支持

| 语言 | 状态 | 文档 |
|------|------|------|
| Python | ✅ 完成 | [STYLE.md](languages/python/STYLE.md) |
| JavaScript | ✅ 完成 | [STYLE.md](languages/javascript/STYLE.md) |
| Go | 🚧 计划中 | - |
| Rust | 🚧 计划中 | - |

---

## 💡 使用场景

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

### 场景 4：选择工具

```
你：根据 vibe-coding 的工具推荐，选择适合我的 AI 编程工具。
```

### 场景 5：新手入门

```
你：我想学习 Vibe Coding，有什么推荐的入门路径吗？
```

---

## 📚 参考

基于以下经典著作和开源项目：

### 书籍

- **Clean Code** - Robert C. Martin
- **Clean Architecture** - Robert C. Martin  
- **The Pragmatic Programmer** - David Thomas
- **Design Patterns** - GoF

### 开源项目

- [程序员鱼皮的 AI 资源大全](https://github.com/liyupi/ai-guide) ⭐ 8.2k
- [Beginner's Vibe Coding Guide](https://github.com/wendy7756/vibe-coding-guide) ⭐ 53
- [氛围编程（Vibe Coding）终极指南](https://github.com/zhanglunet/vibe-coding) ⭐ 34
- [ref-vibecoding](https://github.com/kennyzhu2013/ref-vibecoding) ⭐ 0
- [VibeVibe 繁體中文版](https://github.com/godmakereth/vibe-vibe-tw) ⭐ 45

### 核心概念

Vibe Coding 由 OpenAI 联合创始人 **Andrej Karpathy** 提出：

> **"完全沉浸在 AI 的 vibe 中，用自然语言与 AI 对话，AI 生成代码，开发者通过迭代反馈进入心流状态。"**

---

## 🤝 贡献

欢迎补充：

- 更多语言规范（Go、Rust、Java 等）
- 设计模式示例
- 最佳实践案例
- 反模式案例
- 项目模板
- 工具推荐

---

## 📄 License

MIT

---

## 🔗 GitHub

https://github.com/leozhang2056/vibe-coding

---

**⭐ Star this project if it helps you with AI programming!**
