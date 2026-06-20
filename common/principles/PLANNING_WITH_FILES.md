# 文件化规划（Planning with Files）

> 受 [planning-with-files](https://github.com/OthmanAdi/planning-with-files) 启发 —— 将文件系统作为 AI Agent 的**持久化工作记忆**，解决上下文丢失、目标漂移、错误重复等问题。
>
> **核心隐喻：** 上下文窗口 = RAM（易失、有限），文件系统 = 磁盘（持久、无限）→ 重要信息写入文件。

---

## 为什么要文件化规划？

AI Agent 在执行复杂多步任务时，天然存在三个问题：

| 问题 | 表现 | 文件化方案的解决 |
|------|------|------------------|
| **易失记忆** | `/clear` 或上下文窗口溢出后丢失计划 | 计划存在磁盘上，而非仅存在于对话上下文 |
| **目标漂移** | 50+ 次工具调用后原始目标被稀释 | 每次决策前重新读取计划文件，刷新注意力 |
| **错误重复** | 失败不做记录，下次撞同一条坑 | 所有错误记录到文件，作为知识积累 |

---

## 三文件模式（3-File Pattern）

对于任何需要 3 步以上的复杂任务，在项目根目录创建三个文件：

### `task_plan.md` —— 路线图

分阶段规划，每阶段包含待办清单、当前状态和决策记录。

```markdown
# Task Plan: [任务简称]

## Goal
[一句话描述最终目标]

## Current Phase
Phase 1

## Phases

### Phase 1: [阶段名]
- [ ] 子任务 A
- [ ] 子任务 B
- **Status:** in_progress  <!-- pending | in_progress | complete -->

### Phase 2: [阶段名]
- [ ] 子任务 C
- **Status:** pending

## Decisions Made
| Decision | Rationale |
|----------|-----------|
| 用 X 方案 | 因为…… |

## Errors Encountered
| Error | Attempt | Resolution |
|-------|---------|------------|
|       | 1       |            |
```

### `findings.md` —— 知识库

存放调研、发现、技术决策、资源链接。

```markdown
# Findings & Decisions

## Research Findings
- [关键发现 1]
- [关键发现 2]

## Technical Decisions
| Decision | Rationale |
|----------|-----------|
|          |           |

## Resources
- [URL 或文件路径]
```

### `progress.md` —— 日志

按阶段记录做了什么、文件变动、测试结果、错误详情。

```markdown
# Progress Log

## Session: [日期]

### Phase 1: [阶段名]
- **Status:** in_progress
- Actions taken:
  - [具体操作]
- Files created/modified:
  - [文件路径]

## Test Results
| Test | Input | Expected | Actual | Status |
|------|-------|----------|--------|--------|

## Error Log
| Timestamp | Error | Attempt | Resolution |
|-----------|-------|---------|------------|
```

---

## 七条核心规则

| # | 规则 | 说明 |
|---|------|------|
| 1 | **先创建计划** | 无 `task_plan.md` 不开始复杂任务，不可协商 |
| 2 | **2 动作原则** | 每 2 次查看/浏览器/搜索操作后，**立即**将关键发现写入 `findings.md`（防止多模态信息丢失） |
| 3 | **决策前重读** | 重大决策前重读计划文件，将目标刷新到注意力窗口 |
| 4 | **执行后更新** | 每阶段完成后：更新状态 `in_progress→complete`、记录错误、记录文件变更 |
| 5 | **记录所有错误** | 每一条错误都记入计划文件，防止重复犯错 |
| 6 | **不重复失败路径** | `if action_failed: next_action != same_action` —— 追踪尝试历史，更换方法 |
| 7 | **完成后继续** | 所有阶段完成但用户有追加需求时：追加新阶段到 `task_plan.md`，在 `progress.md` 记录新会话 |

---

## 3 次打击错误协议（3-Strike Error Protocol）

```
第 1 次：诊断并修复
  → 仔细阅读错误信息
  → 识别根因
  → 应用针对性修复

第 2 次：换方案
  → 相同错误？换不同方法
  → 不同工具？不同库？
  → 绝不重复完全相同的失败操作

第 3 次：全局反思
  → 质疑假设
  → 搜索解决方案
  → 考虑更新计划

3 次后仍失败 → 向用户升级报告
  → 说明已尝试的内容
  → 分享具体的错误
  → 请求指导
```

---

## 5 问重启测试（5-Question Reboot Test）

上下文可能丢失时，回答这 5 个问题验证状态是否完整：

| 问题 | 答案来源 |
|------|----------|
| 我在哪？ | `task_plan.md` 中的 Current Phase |
| 我要去哪？ | 剩余未完成阶段 |
| 目标是什么？ | `task_plan.md` 中的 Goal |
| 我学到了什么？ | `findings.md` |
| 我做了什么？ | `progress.md` |

若都能回答 → 上下文完整，可以继续。

---

## 读取 vs 写入决策矩阵

| 场景 | 操作 | 原因 |
|------|------|------|
| 刚写完文件 | **不读** | 内容仍在上下文中 |
| 查看了图片/PDF | **立即写入 findings.md** | 多模态→文本，防止丢失 |
| 浏览器返回了数据 | **写入文件** | 截图不持久化 |
| 开始新阶段 | **读取 plan / findings** | 上下文可能已过期 |
| 遇到错误 | **读取相关文件** | 需要当前状态来修复 |
| 中断后恢复 | **读取所有规划文件** | 恢复状态 |

---

## 反模式（Anti-Patterns）

| 不要做 | 应该做 |
|--------|--------|
| 用 TodoWrite 工具做持久化（易失） | 创建 `task_plan.md` 文件（持久） |
| 目标说一次就忘 | 决策前重读计划 |
| 隐藏错误、静默重试 | 记入错误表格 |
| 一切塞进上下文 | 大块内容存文件 |
| 立即开始执行 | 先创建计划文件 |
| 重复相同失败操作 | 追踪尝试次数，更换方法 |
| 将网页内容直接写入 `task_plan.md` | 外部内容写入 `findings.md`（因为 `task_plan.md` 会被钩子自动读取） |

---

## 何时使用

**使用文件化规划：**
- 多步任务（3 步以上）
- 调研任务
- 构建/创建项目
- 需要大量工具调用的工作
- 任何需要组织性的工作

**跳过：**
- 简单问答
- 单文件修改
- 快速查询

---

## 与 vibe-coding 其他原则的关系

| 文档 | 关系 |
|------|------|
| [AGENT_HIGH_AGENCY.md](./AGENT_HIGH_AGENCY.md) | 规划文件是闭环的证据载体；3-Strike 协议与穷尽排障清单互补 |
| [WORKFLOW.md](../WORKFLOW.md) | 文件化规划是工作流中「规划阶段」的具体实现 |
| [FIRST_PRINCIPLES.md](./FIRST_PRINCIPLES.md) | 先厘清问题与约束再写计划，避免盲目开工 |
| [AI_PROGRAMMING.md](./AI_PROGRAMMING.md) | 渐进开发 + 文件化规划 = 可恢复的增量开发流程 |

---

## 检查清单

开始复杂任务前：

- [ ] 是否创建了 `task_plan.md`（含 Goal、Phases、Status）？
- [ ] 是否创建了 `findings.md`（存放调研结果）？
- [ ] 是否创建了 `progress.md`（记录执行日志）？
- [ ] 当前阶段是否标记了 `in_progress`？
- [ ] 错误是否已记录到错误表，且未重复相同失败路径？
- [ ] 过去 2 次查看/搜索后是否更新了 findings？

---

> 本原则改编自 [planning-with-files](https://github.com/OthmanAdi/planning-with-files)（MIT License），原作者 Ahmad Othman Ammar Adi。
