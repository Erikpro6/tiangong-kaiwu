# 天工开物 · Tiangong Kaiwu

> **经过实战验证的多 AI Agent 协作协议。** 来自 276 次提交、109 个任务批次、5 个协议版本的真实产品开发。

**[English](./README.md)** | **中文**

[![Practices](https://img.shields.io/badge/实践-10-blue)](./practices)
[![Templates](https://img.shields.io/badge/模板-3-green)](./templates)
[![Protocol](https://img.shields.io/badge/协议-v5-orange)](./cases/voiceos-harness.md)
[![License](https://img.shields.io/badge/许可证-MIT-black)](./LICENSE)

> *1637 年，宋应星著《天工开物》——一部记录百工造物的百科全书。389 年后，同一原理驱动 AI Agent 协作交付生产级软件。*

---

## 问题在哪

你在用 AI Agent（Claude Code、Cursor、Copilot）开发软件，可能试过：

- **"凭感觉写"** — 做原型很爽，做正式产品一团乱
- **一个 Agent 一条指令** — 小任务没问题，大了就失控
- **多个 Agent 没规则** — 互相覆盖代码、跳过测试、偏离需求

从"AI 会写代码"到"AI 能交付产品"，差的是**协调**。

## 这是什么

一套经过实战检验的**实践、模板和案例**，用于协调多个 AI Agent 协作开发生产级软件。不是框架，不是库——是一套**协议**，复制到你的项目就能用。

```
你（人类）
  ↓ 意图
指挥者 Agent  → 写 Spec（任务规格书）
  ↓ Spec
执行者 Agent  → 编码、构建、提交
  ↓ 代码
审核者 Agent  → 对照 Spec 审计
  ↓ ✅/❌
部署者        → 上线
```

### 核心原则

1. **文件是唯一沟通方式** — Agent 之间不聊天，通过文件传递信息
2. **人是最终决策者** — AI 提方案，人确认后才执行
3. **角色边界严格，任务可以拆** — 跨领域时拆任务，不放宽规则
4. **机器验证而非肉眼看** — 每个任务附带可执行的验证命令

---

## 实战数据

来自 [VoiceOS 3.0](./cases/voiceos-harness.md) 项目（主要案例研究）：

| 指标 | 数据 |
|------|------|
| 追踪的任务批次 | 109 个，零遗漏 |
| 协议版本演进 | v1 → v5，自进化 |
| 一次通过率 | 33% → **100%**（5 轮进化后） |
| 峰值产出 | 36 小时完成 22 个批次 |
| 并行执行文件冲突 | **0**（引入 Spec Locks 后） |
| 角色越界违规 | **0**（引入拆分模式后） |

---

## 快速开始

### 1. 复制模板

```bash
# 复制主协议文件到项目根目录
cp templates/AGENTS-template.md YOUR_PROJECT/AGENTS.md

# 创建必要目录
mkdir -p YOUR_PROJECT/docs/{specs,reviews,optimizations}

# 复制模板
cp templates/spec-template.md YOUR_PROJECT/docs/specs/_TEMPLATE.md
cp templates/review-template.md YOUR_PROJECT/docs/reviews/_TEMPLATE.md
```

### 2. 选择角色

不需要全部 6 个角色，按需选择：

| 场景 | 所需角色 |
|------|----------|
| 个人开发者 + AI | **指挥者** + **执行者**（2 个角色即可） |
| 2 人（产品+技术） | 指挥者 + 执行者 + **审核者** |
| 3 人以上团队 | 全部 6 个角色 |

### 3. 创建任务看板

创建 `docs/progress.md`，使用 Emoji 状态机：

```markdown
| Batch | 状态 | 内容 | Spec |
|-------|------|------|------|
| B1 | 🔴未开始 | 搭建认证系统 | specs/batch-1 |
| B2 | 🔴未开始 | 仪表盘 UI | specs/batch-2 |
```

状态流转：`🔴 → 🟡 → 🟢 → ✅ → 🔵`（或 `❌` 打回 🟡）

### 4. 开始工作

让你的 AI Agent 读取 `AGENTS.md` 并开始。详见 [迁移指南](./MIGRATION-GUIDE.md)。

---

## 实践清单

### 基础协作

| 实践 | 解决什么问题 |
|------|-------------|
| [文件驱动角色](./practices/file-driven-roles.md) | Agent 通过文件协调，不通过对话 |
| [Emoji 状态机](./practices/emoji-state-machine.md) | 一个 Markdown 文件追踪所有任务状态——不需要 Jira |
| [结构化启动](./practices/harness-initializer.md) | Agent 开工前必须确认环境干净 |

### Spec 工程化

| 实践 | 解决什么问题 |
|------|-------------|
| [Spec 验证命令](./practices/spec-verify-cmd.md) | 可执行的 bash 命令替代"我检查过了，没问题" |
| [Spec 文件锁](./practices/spec-locks.md) | 每个任务声明改哪些文件 → 并行执行零冲突 |
| [Spec 分级](./practices/spec-weight-classes.md) | Micro / Standard / Epic — 规格书的重量匹配任务的重量 |

### 质量保障与进化

| 实践 | 解决什么问题 |
|------|-------------|
| [角色边界执行](./practices/role-boundary-enforcement.md) | 跨领域时拆分任务，不模糊角色边界 |
| [自进化协议](./practices/self-evolving-protocol.md) | 每次错误变成一条规则——协议自己会进步 |
| [增量提交](./practices/incremental-commit.md) | 每 1-3 个 Part 提交一次，不是全做完才提交 |
| [命名规范](./practices/naming-conventions.md) | 文件名、提交信息、状态码、标识符的一页式速查表 |

---

## 运作方式

### 一个任务的完整生命周期

```
  指挥者写 Spec（含文件锁 + 验证命令）
         ↓
  执行者读 Spec → 逐 Part 编码
         ↓（每个 Part）
  执行者运行验证命令 → 提交 commit
         ↓（所有 Part 完成）
  执行者标记 🟢 待审核
         ↓
  审核者对照 Spec 审计代码
         ↓
  ✅ 通过 → 指挥者部署 → 🔵 已上线
  ❌ 打回 → 回到 🟡，执行者修复
```

### 自进化循环

```
  出了问题
      ↓
  指挥者记录：发生了什么 → 根因分析 → 新规则
      ↓
  协议更新 → 版本升级（v4 → v5）
      ↓
  同类问题不再发生
```

VoiceOS 3.0 开发过程中记录了 **11 次进化**，每次都永久消除了一类问题。

---

## 仓库结构

```
├── practices/          ← 10 个可复用实践（按需选用）
├── templates/          ← 3 个即用模板
│   ├── AGENTS-template.md      ← 主协议文件（复制到项目根目录）
│   ├── spec-template.md        ← 任务规格书模板
│   └── review-template.md      ← 代码审核模板
├── cases/              ← 真实案例研究
│   └── voiceos-harness.md      ← 109 个批次的进化故事
├── MIGRATION-GUIDE.md  ← 逐步迁移指南
└── README.md           ← 英文版
```

---

## 常见问题

**必须用全部 9 个实践吗？**
不用。从"文件驱动角色 + Emoji 状态机"开始，遇到问题再加。[迁移指南](./MIGRATION-GUIDE.md)有最小化方案。

**只能用在 Claude Code 上吗？**
不。协议与 Agent 无关——任何能读文件、跑 shell 命令的 AI 编码 Agent 都行（Claude Code、Cursor、Copilot CLI 等）。模板用的是 Claude Code 惯例，改起来很简单。

**这是框架还是库？**
都不是。是一组 Markdown 文件，复制到项目里就行。零依赖、零安装、零绑定。

**我是独立开发者，能用吗？**
最小可用方案是 2 个角色：指挥者（你 + AI）和执行者（AI）。[迁移指南](./MIGRATION-GUIDE.md)覆盖了这个场景。

**和 Prompt Engineering 有什么区别？**
Prompt Engineering 优化单个 Agent 的输出。这优化多个 Agent 之间的协作。不同的技术层。

---

## 来源

所有实践均提炼自 [VoiceOS 3.0](https://voiceos-3-web.pages.dev)，一个完全由多 Agent 协作构建的生产级 AI 认知操作系统：

- **276 次 git commit**，由 6 个专业 AI 角色完成
- **109 个任务批次**，零遗漏追踪
- 协议在 3 周内从 **v1 演进到 v5**
- 一次通过率从 **33% 提升到 100%**
- 引入 Spec Locks 后 **零文件冲突**

完整案例研究 → [VoiceOS 3.0 Harness 演化](./cases/voiceos-harness.md)

---

## 为什么叫"天工开物"？

**天工开物**，明代宋应星（1637 年）著，是中国第一部综合性科技百科全书。书中记录了 18 个行业——从冶金到造船到丝绸——的工匠如何通过系统化流程和分工协作，将原材料转化为精美成品。

这个名字是有意为之：正如宋应星观察到伟大作品并非来自个人天才，而是来自**清晰的角色分工、系统化的流程和积累的工艺知识**，这个项目证明 AI Agent 在同样条件下也能产出最佳成果。

天工，意为"巧夺天工"——精妙到仿佛天赐。开物，意为"开物成务"——将新事物创造出来。

合在一起：**通过有序协作，创造非凡之物。**

---

## 许可证

MIT — 自由使用，请注明出处。
