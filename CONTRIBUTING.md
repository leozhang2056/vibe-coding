# 贡献指南 / Contributing

[中文](#中文) · [English](#english)

---

## 中文

感谢你愿意改进 **vibe-coding**。

### 流程

1. Fork [leozhang2056/vibe-coding](https://github.com/leozhang2056/vibe-coding)  
2. 新建分支：`git checkout -b feat/your-topic`  
3. 修改文档，保持与现有 Markdown 风格一致  
4. 提交并推送到你的 Fork，发起 **Pull Request**  

### 放置规则

| 内容 | 路径 |
|------|------|
| 通用、语言无关原则 | `common/principles/` |
| 设计模式 / 反模式 | `common/patterns/`、`common/anti-patterns/` |
| 某语言专用规范 | `languages/[语言]/` |
| Cursor 常驻规则 | `.cursor/rules/`（`alwaysApply` 需谨慎） |

### 文档质量

- 使用 Markdown，**好的 / 坏的** 代码示例成对出现（若适用）  
- 新增原则时，在 `README.md` 与 `README_CN.md` 的文档树、核心文档表中同步登记  
- 重大变更请更新 [CHANGELOG.md](./CHANGELOG.md)  

### 许可

贡献一经合并，将在 [MIT License](./LICENSE) 下发布（与仓库一致）。

---

## English

Thanks for helping improve **vibe-coding**.

### Workflow

1. Fork the repository  
2. Create a branch: `git checkout -b feat/your-topic`  
3. Edit docs; match existing Markdown style  
4. Push and open a **Pull Request**  

### Where to put changes

| Content | Path |
|---------|------|
| Universal principles | `common/principles/` |
| Patterns / anti-patterns | `common/patterns/`, `common/anti-patterns/` |
| Language-specific guides | `languages/[language]/` |
| Cursor rules | `.cursor/rules/` |

### Quality

- Prefer paired good/bad examples where helpful  
- Update `README.md` and `README_CN.md` (tree + tables) for new principle docs  
- Record notable changes in [CHANGELOG.md](./CHANGELOG.md)  

### License

By contributing, you agree your contributions are licensed under the [MIT License](./LICENSE).
