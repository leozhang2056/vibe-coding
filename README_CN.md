# Vibe Coding - AI 编程约束系统

> 给 AI Agent 提供编程约束指导，确保代码风格可控、架构清晰。

[English](./README.md) | 中文版

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

> 请先阅读 /path/to/vibe-coding/ 项目，理解编程约束和风格指南。

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
├── languages/                 # 语言特定指南
│   ├── python/               # Python
│   │   ├── STYLE.md              # PEP 8、类型注解、文档字符串
│   │   └── README.md
│   ├── javascript/           # JavaScript
│   │   ├── STYLE.md              # ESLint、async/await、函数式编程
│   │   └── README.md
│   ├── java/                 # Java
│   │   ├── ALIBABA_STANDARDS.md  # 阿里巴巴 Java 规范（泰山版）
│   │   ├── DEVELOPMENT.md        # 开发规范
│   │   ├── UTILITIES.md          # 常用工具类
│   │   ├── CLAUDE.md             # AI 提示词模板
│   │   └── README.md
│   ├── android/             # Android (Kotlin)
│   │   ├── ANDROID_PATTERNS.md   # Android 开发模式与最佳实践
│   │   ├── ANDROID_DEVELOPMENT.md
│   │   ├── CONFIGURATION.md
│   │   ├── CLAUDE.md
│   │   └── README.md
│   ├── go/                   # Go（计划中）
│   └── rust/                 # Rust（计划中）
│
└── README.md                 # 本文件
```

---

## 🎯 核心特性

### 1. 分层结构

- **通用原则**位于根目录，适用于所有语言
- **语言特定规则**在子目录中，扩展或覆盖通用规则
- 清晰的继承关系，便于查找相关指南

### 2. 行业标准集成

- **阿里巴巴 Java 开发手册**（泰山版）- 13 个核心问题
- **Clean Code** 原则 - Robert C. Martin
- **SOLID** 设计原则
- **DRY、KISS、YAGNI** 实践
- **Android 开发范例代码大全**模式

### 3. 真实项目模式

从实际项目中提取的模式，包括：
- AutoTask：Python + Playwright 自动化框架
- 真实的 Android 应用程序
- 企业级 Java 应用程序

---

## 📖 使用方法

### 对于 AI Agent

开始新项目时：

```
请先阅读 /root/.openclaw/workspace/vibe-coding/ 以了解：
1. 通用编程原则（common/）
2. 特定语言标准 [语言名称]（languages/[LANGUAGE]/）
3. 查看 AI 提示词模板（languages/[LANGUAGE]/CLAUDE.md）

然后按照工作流（common/WORKFLOW.md）开始开发。
```

### 对于开发者

**快速参考：**

1. **新项目？** 阅读 `common/GETTING_STARTED.md`
2. **语言指南？** 查看 `languages/[LANGUAGE]/README.md`
3. **风格问题？** 参考 `common/principles/CLEAN_CODE.md`
4. **架构设计？** 复习 `common/principles/CLEAN_ARCHITECTURE.md`
5. **设计模式？** 查阅 `common/patterns/DESIGN_PATTERNS.md`

**推荐阅读顺序：**

1. `common/principles/AI_PROGRAMMING.md` - AI 编程最佳实践
2. `common/principles/SOLID.md` - 设计原则
3. `common/principles/CLEAN_CODE.md` - 代码质量
4. `languages/[LANGUAGE]/README.md` - 语言特定规则
5. `languages/[LANGUAGE]/CLAUDE.md` - AI 提示词模板

---

## 🔧 核心文档

### 通用原则

| 文档 | 描述 | 状态 |
|------|------|------|
| [SOLID](./common/principles/SOLID.md) | 单一职责、开闭原则、里氏替换、接口隔离、依赖倒置 | ✅ 完成 |
| [CLEAN_CODE](./common/principles/CLEAN_CODE.md) | 有意义的命名、函数、注释、错误处理 | ✅ 完成 |
| [CLEAN_ARCHITECTURE](./common/principles/CLEAN_ARCHITECTURE.md) | 依赖规则、实体、用例、接口 | ✅ 完成 |
| [DRY_KISS_YAGNI](./common/principles/DRY_KISS_YAGNI.md) | 不要重复自己、保持简单、你不会需要它 | ✅ 完成 |
| [AI_PROGRAMMING](./common/principles/AI_PROGRAMMING.md) | AI 特定的最佳实践和模式 | ✅ 完成 |

### 语言特定指南

| 语言 | 核心文档 | 状态 |
|------|----------|------|
| **Python** | [STYLE.md](./languages/python/STYLE.md) - PEP 8、类型注解、文档字符串 | ✅ 完成 |
| **JavaScript** | [STYLE.md](./languages/javascript/STYLE.md) - ESLint、async/await、函数式编程 | ✅ 完成 |
| **Java** | [ALIBABA_STANDARDS.md](./languages/java/ALIBABA_STANDARDS.md) - 阿里巴巴规范（泰山版） | ✅ 完成 |
| **Android** | [ANDROID_PATTERNS.md](./languages/android/ANDROID_PATTERNS.md) - Android 开发模式与最佳实践 | ✅ 完成 |
| **Go** | 计划中 | 🔜 即将推出 |
| **Rust** | 计划中 | 🔜 即将推出 |

---

## 🌟 亮点

### 阿里巴巴 Java 规范（泰山版）

基于官方阿里巴巴 Java 开发手册，涵盖 13 个核心问题：

- ✅ 三目运算符空指针问题
- ✅ HashMap 初始化最佳实践
- ✅ 线程池规范（避免使用 Executors）
- ✅ 字符串拼接优化
- ✅ 日期时间处理（避免 static SimpleDateFormat）
- ✅ 序列化规范（serialVersionUID）
- ✅ 数据库规范（使用 COUNT(*)）
- ✅ 日志最佳实践（使用 SLF4J 门面）

### Android 开发模式

基于《Android 开发范例代码大全》（727 页）：

- ✅ 组件架构（Activity、Service、Broadcast、ContentProvider）
- ✅ Fragment 使用模式
- ✅ UI 模式（自定义窗口、方向、对话框）
- ✅ 多屏幕分辨率适配
- ✅ 数据持久化（SharedPreferences、SQLite、Room）
- ✅ 网络编程（HttpURLConnection、Retrofit）
- ✅ 性能优化（防止内存泄漏、ViewHolder）

### 真实项目模式

从实际项目中提取：

- **AutoTask**：Python + Playwright 自动化框架模式
  - HTTP 客户端抽象
  - 浏览器自动化模式
  - 任务调度和监控
  - 通知系统

---

## 🤖 对于 AI Agent

### 推荐工作流

1. **先阅读**：编码前始终阅读项目结构
2. **遵循层级**：通用原则 → 语言规则 → 项目约定
3. **使用模板**：查看 `CLAUDE.md` 获取 AI 提示词模板
4. **验证**：与真实项目中的示例交叉参考

### 示例提示词

```
在编写代码之前，请：
1. 阅读 /root/.openclaw/workspace/vibe-coding/common/principles/
2. 阅读 /root/.openclaw/workspace/vibe-coding/languages/python/
3. 查看 /root/.openclaw/workspace/auto-reward/ 的 AutoTask 项目模式
4. 将这些标准应用到新代码中

遵循 common/WORKFLOW.md 中的工作流，并使用 languages/python/CLAUDE.md 中的模板
```

---

## 📊 项目状态

| 组件 | 状态 | 最后更新 |
|------|------|----------|
| 通用原则 | ✅ 完成 | 2026-02-28 |
| Python 指南 | ✅ 完成 | 2026-02-28 |
| JavaScript 指南 | ✅ 完成 | 2026-02-28 |
| Java 指南 | ✅ 完成 + 阿里巴巴规范 | 2026-02-28 |
| Android 指南 | ✅ 完成 + 开发模式 | 2026-02-28 |
| Go 指南 | 🔜 计划中 | - |
| Rust 指南 | 🔜 计划中 | - |

---

## 🤝 贡献

欢迎贡献！请：

1. Fork 本仓库
2. 创建特性分支
3. 添加或改进文档
4. 遵循现有的风格和结构
5. 提交 Pull Request

**指南：**
- 语言特定规则放在 `languages/[LANGUAGE]/`
- 通用原则放在 `common/principles/`
- 所有文档使用 Markdown
- 包含代码示例（好的和坏的）
- 添加真实项目中的实用模式

---

## 📝 许可证

本项目是开源的，使用 MIT 许可证。

---

## 🙏 致谢

- **阿里巴巴集团** - Java 开发手册（泰山版）
- **Robert C. Martin** - Clean Code 和 SOLID 原则
- **Martin Fowler** - 设计模式和重构实践
- **Dave Smith & Jeff Friesen** - Android 开发范例代码大全
- **AutoTask 项目** - 真实的 Python 自动化模式

---

## 📞 联系方式

- **GitHub**: https://github.com/leozhang2056/vibe-coding
- **Issues**: https://github.com/leozhang2056/vibe-coding/issues

---

<div align="center">

**⭐ 如果这个项目对你有帮助，请给它一个星标！⭐**

由 Vibe Coding 社区用 ❤️ 制作

</div>
