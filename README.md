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
├── .cursor/
│   └── rules/                 # Cursor rules (optional)
│       └── vibe-agent-discipline.mdc  # High-agency execution discipline (always-on)
├── common/                    # Universal principles (language-agnostic)
│   ├── principles/           # Core principles
│   │   ├── FIRST_PRINCIPLES.md    # First Principles thinking
│   │   ├── AGENT_HIGH_AGENCY.md   # High-agency discipline (PUA-aligned)
│   │   ├── PLANNING_WITH_FILES.md # File-based planning (3-file pattern)
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
│   │   └── README.md
│   └── rust/                 # Rust (planned)
│       └── README.md
│
├── AGENTS.md                 # Entry notes for AI agents
├── CHANGELOG.md              # Changelog
├── CONTRIBUTING.md           # Contributing guide
├── LICENSE                   # MIT License
└── README.md                 # This file
```

---

## 🎯 Core Features

### 1. Hierarchical Structure

- **Universal principles** at the root apply to all languages
- **Language-specific rules** in subdirectories extend or override general rules
- Clear inheritance relationship makes it easy to find relevant guidelines

### 2. Industry Standards Integration

- **File-based planning** — Context = RAM, Filesystem = Disk. Persistent 3-file pattern (`task_plan.md`, `findings.md`, `progress.md`). (Inspired by [planning-with-files](https://github.com/OthmanAdi/planning-with-files))
- **Alibaba Java Development Manual** (Taishan Edition) - 13 core questions
- **First Principles** – reason from fundamentals; avoid bandwagon decisions
- **Clean Code** principles by Robert C. Martin
- **SOLID** design principles
- **DRY, KISS, YAGNI** practices
- **High-agency execution discipline** (aligned with [PUA Skill](https://github.com/leozhang2056/pua)) — close the loop, fact-driven, exhaust debugging paths
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
1. Universal programming principles (common/), including high-agency discipline (common/principles/AGENT_HIGH_AGENCY.md)
2. Planning with files for multi-step tasks (common/principles/PLANNING_WITH_FILES.md)
3. Language-specific standards for [LANGUAGE] (languages/[LANGUAGE]/)
4. Review the AI prompt template (languages/[LANGUAGE]/CLAUDE.md)

Then follow the workflow (common/WORKFLOW.md) to begin development. If using Cursor, also apply rules under .cursor/rules/.
```

### For Developers

**Quick Reference:**

1. **New Project?** Read `common/GETTING_STARTED.md`
2. **Planning?** See `common/principles/PLANNING_WITH_FILES.md` (3-file pattern)
3. **Language Guide?** Check `languages/[LANGUAGE]/README.md`
4. **Style Issues?** See `common/principles/CLEAN_CODE.md`
5. **Architecture?** Review `common/principles/CLEAN_ARCHITECTURE.md`
6. **Design Patterns?** Reference `common/patterns/DESIGN_PATTERNS.md`
7. **Agent gives up / skips verification?** Read `common/principles/AGENT_HIGH_AGENCY.md`

**Recommended Reading Order:**

1. `common/principles/FIRST_PRINCIPLES.md` - First principles (thinking foundation)
2. `common/principles/AGENT_HIGH_AGENCY.md` - High-agency discipline (close loop & debugging)
3. `common/principles/AI_PROGRAMMING.md` - AI programming best practices
4. `common/principles/PLANNING_WITH_FILES.md` - File-based planning (persistent context)
5. `common/principles/SOLID.md` - Design principles
6. `common/principles/CLEAN_CODE.md` - Code quality
7. `languages/[LANGUAGE]/README.md` - Language specifics
8. `languages/[LANGUAGE]/CLAUDE.md` - AI prompt template

---

## 🔧 Key Documents

### Universal Principles

| Document | Description | Status |
|----------|-------------|--------|
| [FIRST_PRINCIPLES](./common/principles/FIRST_PRINCIPLES.md) | First-principles thinking: reason from facts, question assumptions, avoid bandwagon | ✅ Complete |
| [AGENT_HIGH_AGENCY](./common/principles/AGENT_HIGH_AGENCY.md) | High-agency execution: three red lines, anti-lazy patterns, 7-point checklist ([PUA](https://github.com/leozhang2056/pua)-aligned) | ✅ Complete |
| [PLANNING_WITH_FILES](./common/principles/PLANNING_WITH_FILES.md) | File-based planning: 3-file pattern, 2-action rule, 3-strike error protocol ([planning-with-files](https://github.com/OthmanAdi/planning-with-files)-inspired) | ✅ Complete |
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
2. **Plan First**: For multi-step tasks, create `task_plan.md`, `findings.md`, `progress.md` before coding
3. **Follow Hierarchy**: Universal principles → Language rules → Project conventions
4. **Use Templates**: Check `CLAUDE.md` for AI prompt templates
5. **Verify**: Cross-reference against examples in real projects

### Example Prompt

```
Before writing code, please:
1. Read /root/.openclaw/workspace/vibe-coding/common/principles/ (focus: AGENT_HIGH_AGENCY.md, FIRST_PRINCIPLES.md, PLANNING_WITH_FILES.md)
2. Read /root/.openclaw/workspace/vibe-coding/languages/python/
3. Review the AutoTask project patterns at /root/.openclaw/workspace/auto-reward/
4. For multi-step tasks, create task_plan.md, findings.md, and progress.md first
5. Apply these standards to the new code; close the loop before claiming done (tests/build/logs)

Follow the workflow in common/WORKFLOW.md and use the template in languages/python/CLAUDE.md
```

---

## 📊 Project Status

| Component | Status | Last Updated |
|-----------|--------|--------------|
| Universal principles | ✅ Complete (incl. PUA-aligned AGENT_HIGH_AGENCY + PLANNING_WITH_FILES) | 2026-06-20 |
| Python guide | ✅ Complete | 2026-03-08 |
| JavaScript guide | ✅ Complete | 2026-03-08 |
| Java guide | ✅ Complete + Alibaba standards | 2026-03-08 |
| Android guide | ✅ Complete + Patterns | 2026-03-08 |
| Go guide | 🔜 Planned (README placeholder) | 2026-03-08 |
| Rust guide | 🔜 Planned (README placeholder) | 2026-03-08 |

---

## 🤝 Contributing

See **[CONTRIBUTING.md](./CONTRIBUTING.md)** for the full flow. Summary:

1. Fork the repository
2. Create a feature branch
3. Add or improve documentation
4. Follow the existing style and structure
5. Open a pull request; **update [CHANGELOG.md](./CHANGELOG.md) for notable changes**

**Guidelines:**
- Keep language-specific rules in `languages/[LANGUAGE]/`
- Universal principles go in `common/principles/`; Cursor always-on rules go in `.cursor/rules/`
- Use Markdown for all documentation
- Include code examples (both good and bad)
- Add practical patterns from real projects

---

## 📝 License

Open source under the **[MIT License](./LICENSE)**.

---

## 🙏 Acknowledgments

- **Alibaba Group** - Java Development Manual (Taishan Edition)
- **Robert C. Martin** - Clean Code and SOLID principles
- **Martin Fowler** - Design patterns and refactoring practices
- **Dave Smith & Jeff Friesen** - Android Development Recipes
- **AutoTask Project** - Real-world Python automation patterns
- **PUA Skill** ([leozhang2056/pua](https://github.com/leozhang2056/pua)) - High-agency agent behavior design, distilled into `AGENT_HIGH_AGENCY.md`
- **Ahmad Othman Ammar Adi** - [planning-with-files](https://github.com/OthmanAdi/planning-with-files) (MIT License), the file-based planning pattern adapted into `PLANNING_WITH_FILES.md`

---

## 📞 Contact

- **GitHub**: https://github.com/leozhang2056/vibe-coding
- **Issues**: https://github.com/leozhang2056/vibe-coding/issues

---

<div align="center">

**⭐ If you find this project helpful, please give it a star! ⭐**

Made with ❤️ by the Vibe Coding community

</div>
