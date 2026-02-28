# Vibe Coding - AI Programming Constraint System

> Provide programming constraint guidance for AI Agents to ensure code style consistency and architectural clarity.

[中文版](./README_CN.md) | English (Default)

## Project Goals

Centralize best practices, architectural principles, and style guidelines to ensure AI produces high-quality code that meets standards before coding begins.

---

## 🚀 Quick Start

### 1. Clone the Project

```bash
git clone https://github.com/leozhang2056/vibe-coding.git
cd vibe-coding
```

### 2. Let AI Read

Before starting a new project:

> Please read the /path/to/vibe-coding/ project first to understand the programming constraints and style guidelines.

---

## 📚 Document Structure

```
vibe-coding/
├── common/                    # Universal principles (language-agnostic)
│   ├── principles/           # Core principles
│   │   ├── SOLID.md               # Single responsibility, Open-closed, etc.
│   │   ├── CLEAN_CODE.md          # Clean Code principles
│   │   ├── CLEAN_ARCHITECTURE.md  # Clean Architecture
│   │   ├── DRY_KISS_YAGNI.md      # DRY, KISS, YAGNI principles
│   │   └── AI_PROGRAMMING.md      # AI Programming best practices
│   ├── patterns/             # Design patterns
│   │   └── DESIGN_PATTERNS.md     # Factory, Strategy, Observer, etc.
│   ├── anti-patterns/        # Anti-patterns
│   │   └── ANTI_PATTERNS.md       # Magic numbers, nested hell, etc.
│   ├── WORKFLOW.md            # Vibe Coding workflow
│   ├── TOOLS.md               # AI programming tools
│   ├── PROJECT_STRUCTURE.md   # Project organization
│   ├── GETTING_STARTED.md     # Getting started guide
│   └── QUICK_REFERENCE.md     # Quick reference guide
│
├── languages/                 # Language-specific guides
│   ├── python/               # Python
│   │   ├── STYLE.md              # PEP 8, type hints, docstrings
│   │   └── README.md
│   ├── javascript/           # JavaScript
│   │   ├── STYLE.md              # ESLint, async/await, FP
│   │   └── README.md
│   ├── java/                 # Java
│   │   ├── ALIBABA_STANDARDS.md  # Alibaba Java standards (Taishan Edition)
│   │   ├── DEVELOPMENT.md        # Development standards
│   │   ├── UTILITIES.md          # Common utilities
│   │   ├── CLAUDE.md             # AI prompt template
│   │   └── README.md
│   ├── android/             # Android (Kotlin)
│   │   ├── ANDROID_PATTERNS.md   # Android patterns & best practices
│   │   ├── ANDROID_DEVELOPMENT.md
│   │   ├── CONFIGURATION.md
│   │   ├── CLAUDE.md
│   │   └── README.md
│   ├── go/                   # Go (planned)
│   └── rust/                 # Rust (planned)
│
└── README.md                 # This file
```

---

## 🎯 Core Features

### 1. Hierarchical Structure

- **Universal principles** at the root apply to all languages
- **Language-specific rules** in subdirectories extend or override general rules
- Clear inheritance relationship makes it easy to find relevant guidelines

### 2. Industry Standards Integration

- **Alibaba Java Development Manual** (Taishan Edition) - 13 core questions
- **Clean Code** principles by Robert C. Martin
- **SOLID** design principles
- **DRY, KISS, YAGNI** practices
- **Android Development Recipes** patterns

### 3. Real Project Patterns

Extracted patterns from actual projects like:
- AutoTask: Python + Playwright automation framework
- Real-world Android applications
- Enterprise Java applications

---

## 📖 How to Use

### For AI Agents

When starting a new project:

```
Please read /root/.openclaw/workspace/vibe-coding/ first to understand:
1. Universal programming principles (common/)
2. Language-specific standards for [LANGUAGE] (languages/[LANGUAGE]/)
3. Review the AI prompt template (languages/[LANGUAGE]/CLAUDE.md)

Then follow the workflow (common/WORKFLOW.md) to begin development.
```

### For Developers

**Quick Reference:**

1. **New Project?** Read `common/GETTING_STARTED.md`
2. **Language Guide?** Check `languages/[LANGUAGE]/README.md`
3. **Style Issues?** See `common/principles/CLEAN_CODE.md`
4. **Architecture?** Review `common/principles/CLEAN_ARCHITECTURE.md`
5. **Design Patterns?** Reference `common/patterns/DESIGN_PATTERNS.md`

**Recommended Reading Order:**

1. `common/principles/AI_PROGRAMMING.md` - AI programming best practices
2. `common/principles/SOLID.md` - Design principles
3. `common/principles/CLEAN_CODE.md` - Code quality
4. `languages/[LANGUAGE]/README.md` - Language specifics
5. `languages/[LANGUAGE]/CLAUDE.md` - AI prompt template

---

## 🔧 Key Documents

### Universal Principles

| Document | Description | Status |
|----------|-------------|--------|
| [SOLID](./common/principles/SOLID.md) | Single responsibility, Open-closed, Liskov substitution, Interface segregation, Dependency inversion | ✅ Complete |
| [CLEAN_CODE](./common/principles/CLEAN_CODE.md) | Meaningful names, functions, comments, error handling | ✅ Complete |
| [CLEAN_ARCHITECTURE](./common/principles/CLEAN_ARCHITECTURE.md) | Dependency rules, entities, use cases, interfaces | ✅ Complete |
| [DRY_KISS_YAGNI](./common/principles/DRY_KISS_YAGNI.md) | Don't repeat yourself, Keep it simple, You aren't gonna need it | ✅ Complete |
| [AI_PROGRAMMING](./common/principles/AI_PROGRAMMING.md) | AI-specific best practices and patterns | ✅ Complete |

### Language-Specific Guides

| Language | Key Documents | Status |
|----------|---------------|--------|
| **Python** | [STYLE.md](./languages/python/STYLE.md) - PEP 8, type hints, docstrings | ✅ Complete |
| **JavaScript** | [STYLE.md](./languages/javascript/STYLE.md) - ESLint, async/await, FP | ✅ Complete |
| **Java** | [ALIBABA_STANDARDS.md](./languages/java/ALIBABA_STANDARDS.md) - Alibaba standards (Taishan Edition) | ✅ Complete |
| **Android** | [ANDROID_PATTERNS.md](./languages/android/ANDROID_PATTERNS.md) - Android patterns & best practices | ✅ Complete |
| **Go** | Planned | 🔜 Coming soon |
| **Rust** | Planned | 🔜 Coming soon |

---

## 🌟 Highlights

### Alibaba Java Standards (Taishan Edition)

Based on the official Alibaba Java Development Manual, covering 13 core questions:

- ✅ Ternary operator NPE issues
- ✅ HashMap initialization best practices
- ✅ Thread pool standards (avoid Executors)
- ✅ String concatenation optimization
- ✅ Date/Time handling (avoid static SimpleDateFormat)
- ✅ Serialization guidelines (serialVersionUID)
- ✅ Database standards (use COUNT(*))
- ✅ Logging best practices (use SLF4J facade)

### Android Development Patterns

Based on "Android Development Recipes" (727 pages):

- ✅ Component architecture (Activity, Service, Broadcast, ContentProvider)
- ✅ Fragment usage patterns
- ✅ UI patterns (custom windows, orientation, dialogs)
- ✅ Multi-screen resolution adaptation
- ✅ Data persistence (SharedPreferences, SQLite, Room)
- ✅ Network programming (HttpURLConnection, Retrofit)
- ✅ Performance optimization (memory leak prevention, ViewHolder)

### Real Project Patterns

Extracted from actual projects:

- **AutoTask**: Python + Playwright automation framework patterns
  - HTTP client abstraction
  - Browser automation patterns
  - Task scheduling and monitoring
  - Notification systems

---

## 🤖 For AI Agents

### Recommended Workflow

1. **Read First**: Always read the project structure before coding
2. **Follow Hierarchy**: Universal principles → Language rules → Project conventions
3. **Use Templates**: Check `CLAUDE.md` for AI prompt templates
4. **Verify**: Cross-reference against examples in real projects

### Example Prompt

```
Before writing code, please:
1. Read /root/.openclaw/workspace/vibe-coding/common/principles/
2. Read /root/.openclaw/workspace/vibe-coding/languages/python/
3. Review the AutoTask project patterns at /root/.openclaw/workspace/auto-reward/
4. Apply these standards to the new code

Follow the workflow in common/WORKFLOW.md and use the template in languages/python/CLAUDE.md
```

---

## 📊 Project Status

| Component | Status | Last Updated |
|-----------|--------|--------------|
| Universal principles | ✅ Complete | 2026-02-28 |
| Python guide | ✅ Complete | 2026-02-28 |
| JavaScript guide | ✅ Complete | 2026-02-28 |
| Java guide | ✅ Complete + Alibaba standards | 2026-02-28 |
| Android guide | ✅ Complete + Patterns | 2026-02-28 |
| Go guide | 🔜 Planned | - |
| Rust guide | 🔜 Planned | - |

---

## 🤝 Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Add or improve documentation
4. Follow the existing style and structure
5. Submit a pull request

**Guidelines:**
- Keep language-specific rules in `languages/[LANGUAGE]/`
- Universal principles go in `common/principles/`
- Use Markdown for all documentation
- Include code examples (both good and bad)
- Add practical patterns from real projects

---

## 📝 License

This project is open source and available under the MIT License.

---

## 🙏 Acknowledgments

- **Alibaba Group** - Java Development Manual (Taishan Edition)
- **Robert C. Martin** - Clean Code and SOLID principles
- **Martin Fowler** - Design patterns and refactoring practices
- **Dave Smith & Jeff Friesen** - Android Development Recipes
- **AutoTask Project** - Real-world Python automation patterns

---

## 📞 Contact

- **GitHub**: https://github.com/leozhang2056/vibe-coding
- **Issues**: https://github.com/leozhang2056/vibe-coding/issues

---

<div align="center">

**⭐ If you find this project helpful, please give it a star! ⭐**

Made with ❤️ by the Vibe Coding community

</div>
