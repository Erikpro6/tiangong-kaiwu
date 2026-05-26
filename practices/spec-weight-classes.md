# 实践: Spec Weight Classes — 三级 Spec 分类体系

> 提炼自 VoiceOS 3.0 Spec 模板迭代

## 问题

所有任务用同一个 Spec 模板，导致：
- 简单任务（改一个按钮颜色）也要写完整 Spec（目标、背景、Parts、verify_cmd），开销远超实际工作量
- 复杂任务（架构级改动）的 Spec 又不够详细，缺少回滚计划和测试要求
- 执行者对简单 Spec 浪费时间，对复杂 Spec 信息不足

## 方案

将 Spec 分为三个重量级，每个级别有不同的模板要求和流程规则：

### Micro Spec（微型）

| 属性 | 值 |
|------|------|
| 长度 | < 30 行 |
| 适用 | 单文件改动，逻辑简单 |
| 格式 | 直接写在 progress.md 的备注列 |
| 流程 | 无需独立文件，无需审核 |
| 示例 | 改按钮颜色、修 typo、调 spacing |

```markdown
# progress.md 中的 Micro Spec
| B106 | 🟢待审核 | 修复登录按钮颜色 | 改 `bg-blue-500` → `bg-primary` | — |
```

### Standard Spec（标准）

| 属性 | 值 |
|------|------|
| 长度 | 50-150 行 |
| 适用 | 多文件改动，有明确 Parts |
| 格式 | 独立 Spec 文件（`specs/batch-N.md`） |
| 流程 | 完整 Spec → 执行 → 审核 |
| 模板 | 目标 / Parts / verify_cmd / Locks |

```markdown
# Standard Spec 结构
# Spec: batch-103
## 目标
## Locks
## Part 1: ...
## Part 2: ...
## verify_cmd
```

### Epic Spec（史诗）

| 属性 | 值 |
|------|------|
| 长度 | 150-300+ 行 |
| 适用 | 架构级改动，多角色协作 |
| 格式 | 独立 Spec 文件 + 回滚计划 |
| 流程 | 完整 Spec → 拆分模式 → 分段 commit → 强制审核 |
| 额外要求 | 回滚计划、依赖图、强制测试 Part |

```markdown
# Epic Spec 额外结构
## 回滚计划
- 如果 Part 3 失败，revert 到 Part 2 的 commit
## 依赖图
- Part 1 → Part 2, Part 3（并行）
- Part 2, Part 3 → Part 4
## 强制测试 Part
- Part N: 集成测试 + E2E 验证
```

### 分类决策树

```
改动涉及 1 个文件？
  ├─ 是 → 逻辑简单？→ Micro
  └─ 否 → 涉及架构变更？
              ├─ 是 → Epic
              └─ 否 → Standard
```

## 为什么有效

- **减少开销**: 简单任务不用写完整 Spec，节省 70%+ 文档时间
- **增加深度**: 复杂任务强制有回滚计划和测试，降低风险
- **分级审核**: Micro 免审、Standard 标准审、Epic 强制审，审核资源用在刀刃上
- **可扩展**: 项目可以自定义更多级别（如 Nano / Mega）

## 适用条件

- 任务复杂度差异大（有改颜色也有改架构的）
- 有 Spec 模板（至少 Standard 级别有模板）
- 有指挥者角色（负责判断任务属于哪个级别）
- 有审核环节（不同级别有不同审核要求）

## 实战证据

VoiceOS 3.0 中，B106/B107 使用 Micro Spec（直接在 progress.md 中一行描述），Spec 文档开销从平均 15 分钟降到 **< 1 分钟**。B109（Epic 级，7 Parts + 拆分模式 + 回滚计划）使用完整 Epic 模板，虽然 Spec 编写耗时 30 分钟，但执行零返工。三级分类让指挥者可以根据任务复杂度灵活调度，而不是一刀切。
