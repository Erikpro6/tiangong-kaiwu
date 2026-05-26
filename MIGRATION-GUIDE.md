# 迁移复用指南

> 将 AI 工作流实践迁移到你的项目

## 第一步: 选择角色

你不需要全部 6 个角色，根据团队规模选择：

| 场景 | 推荐角色 |
|------|----------|
| 个人开发者 + AI | 指挥者 + 执行者 |
| 2 人（产品+技术） | 指挥者 + 执行者 + 审核者 |
| 3+ 人团队 | 全部 6 角色 |

**最小可用**: 指挥者 + 执行者（2 个角色就能跑通整个流程）

## 第二步: 复制模板

```bash
# 1. 复制 AGENTS.md 模板到项目根目录
cp templates/AGENTS-template.md YOUR_PROJECT/AGENTS.md

# 2. 创建目录结构
mkdir -p YOUR_PROJECT/docs/{specs,reviews,optimizations}

# 3. 复制 Spec 模板
cp templates/spec-template.md YOUR_PROJECT/docs/specs/_TEMPLATE.md

# 4. 复制审核报告模板
cp templates/review-template.md YOUR_PROJECT/docs/reviews/_TEMPLATE.md
```

## 第三步: 定制 AGENTS.md

打开 `YOUR_PROJECT/AGENTS.md`，修改以下内容：

1. **工作目录路径** — 替换 `E:\Erik\VoiceOS_3.0_Web` 为你的项目路径
2. **构建命令** — 替换 `npx next build` 为你的构建命令
3. **部署命令** — 替换 wrangler 为你的部署工具
4. **角色选择** — 删除你不需要的角色定义
5. **Git commit 格式** — 根据你的规范调整

## 第四步: 选择实践

从 `practices/` 目录选择适合你的实践：

- **必选**: `file-driven-roles.md`（文档驱动协作，这是基础）
- **推荐**: `spec-verify-cmd.md`（可执行验证）
- **推荐**: `incremental-commit.md`（增量提交）
- **可选**: `harness-initializer.md`（结构化启动）

每个实践文件包含：
- 问题（为什么要用）
- 方案（怎么用）
- 为什么有效（原理）
- 适用条件（你的项目适合不适合）

## 第五步: 开始工作

```
1. 打开第一个窗口: "指挥者"（规划 + 写 Spec）
2. 打开第二个窗口: "执行者"（读 Spec + 编码 + commit）
3. 执行者完成后，指挥者审核
4. （可选）打开审核者窗口独立审核
```

## 常见问题

**Q: 必须用 Claude Code 吗？**
A: 不必须。这套方法论适用于任何支持文件读写和命令执行的 AI 工具（Cursor、Copilot、Gemini CLI 等）。关键是"文档驱动"的理念，不是具体工具。

**Q: Spec 要写多详细？**
A: 新项目建议非常详细（精确到文件路径和代码片段）。团队磨合后可以逐渐精简。详细 Spec 的成本是 5 分钟，但能省下 30 分钟的返工。

**Q: 角色可以合并吗？**
A: 可以。最小配置是指挥者 + 执行者（指挥者兼任审核者）。但建议至少 3 个角色——指挥者不该审核自己的 Spec，这有偏见。

**Q: 进化日志必须写吗？**
A: 强烈建议。没有进化日志，你的协议只会原地踏步。每次复盘花 3 分钟写一条，积累 10 条就是一套成熟的方法论。
