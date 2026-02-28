# 项目组织结构指南

> 基于开源项目的最佳实践

## 推荐的项目结构

```
project-name/
├── README.md              # 项目全貌
├── CONTRIBUTING.md        # 贡献流程
├── LICENSE                # 许可证
├── Makefile               # 封装常用命令
├── .gitignore             # Git 忽略规则
│
├── docs/                  # 文档
│   ├── getting-started.md
│   ├── architecture.md
│   └── api.md
│
├── src/                   # 源代码
│   ├── core/             # 核心业务逻辑
│   ├── models/           # 数据模型
│   ├── services/         # 业务服务
│   └── utils/           # 工具函数
│
├── tests/                 # 测试
│   ├── unit/             # 单元测试
│   ├── integration/      # 集成测试
│   └── e2e/              # 端到端测试
│
├── scripts/               # 脚本
│   ├── setup.sh
│   └── deploy.sh
│
└── config/                # 配置
    ├── development.yaml
    ├── production.yaml
    └── test.yaml
```

---

## 文档组织

### 必备文档

| 文件 | 说明 |
|------|------|
| **README.md** | 项目介绍、快速开始、安装说明 |
| **CONTRIBUTING.md** | 贡献流程、开发规范 |
| **LICENSE** | 开源协议 |
| **CHANGELOG.md** | 版本变更历史 |
| **docs/** | 详细文档 |

### 文档示例

```markdown
# README.md

# 项目名称

简短描述（一句话）

## 功能特性

- 特性 1
- 特性 2

## 快速开始

\`\`\`bash
git clone https://github.com/user/project.git
cd project
npm install
npm start
\`\`\`

## 文档

详细文档：[docs/](docs/)

## 许可证

MIT
```

---

## Makefile 封装常用命令

### 示例

```makefile
.PHONY: help install test lint build clean

help: ## Show this help message
    @echo "Usage: make [target]"
    @echo ""
    @echo "Available targets:"
    @sed -n 's/^.*//## /p;## */p' Makefile | sort

install: ## Install dependencies
    pip install -r requirements.txt
    npm install

test: ## Run tests
    pytest

lint: ## Run linting
    flake8 src
    eslint .

build: ## Build the project
    npm run build

clean: ## Clean build artifacts
    rm -rf dist/ build/
```

---

## Git 提交规范

### Conventional Commits

格式：`<type>(<scope>): <subject>`

**类型：**
- `feat`: 新功能
- `fix`: 修复 bug
- `docs`: 文档更新
- `chore`: 构建/工具更新
- `refactor`: 重构
- `test`: 测试
- `style`: 代码风格

**示例：**

```bash
feat(auth): add JWT token refresh

fix(database): fix connection leak

docs(readme): update installation instructions

refactor(user): simplify user model

test(api): add integration tests
```

---

## 代码审查清单

### 提交前检查

- [ ] 代码通过 lint
- [ ] 测试通过
- [ ] 文档已更新
- [ ] 没有调试代码
- [ ] 没有敏感信息

### PR 描述模板

```markdown
## 变更摘要

简要说明这次 PR 做了什么

## 动机

为什么要做这个变更

## 测试步骤

\`\`\`bash
make test
# 验证步骤
\`\`\`

## 截图

（如果有 UI 变更）
```

---

## 安全与配置

### 敏感信息

```yaml
# ❌ 不要提交
- API 密钥
- 数据库密码
- 私钥文件
- .env 文件

# ✅ 正确做法
使用环境变量
创建 .env.example 模板
在 .gitignore 中忽略
```

### 权限管理

```python
# ✅ 正确
import os

API_KEY = os.getenv('API_KEY')
if not API_KEY:
    raise ValueError("API_KEY not set")
```

---

## AI 编码检查清单

- [ ] 根目录扁平，避免巨石文件
- [ ] 使用 Makefile 封装常用命令
- [ ] 遵循 Conventional Commits
- [ ] 提供完整的文档
- [ ] 包含测试用例
- [ ] 运行 lint 检查
- [ ] 敏感信息不提交
