# 实践: Spec Locks — 声明式文件锁防止并行冲突

> 提炼自 VoiceOS 3.0 并行执行优化

## 问题

多个 AI Agent 并行执行不同任务时，可能同时修改同一个文件，导致：
- 后提交的覆盖先提交的改动
- Build 失败但难以定位哪个 Agent 引入了冲突
- 被迫串行执行所有任务，浪费并行能力

## 方案

借鉴数据库"声明写集"的思想：每个 Spec 在头部声明它要修改的文件列表（Locks），开工前交叉检查是否有重叠。

### Spec 头部格式

```markdown
# Spec: batch-103

## Locks (本 Spec 修改的文件)
- src/components/Dashboard.tsx
- src/lib/api.ts
- src/styles/dashboard.css

## Parts
...
```

### 并行调度规则

```
1. 收集所有待执行 Spec 的 Locks
2. 交叉检查：
   - 无重叠 → 并行执行
   - 有重叠 → 串行执行（被锁的排后面）
3. 执行者只能修改 Locks 声明的文件
4. 如果执行中发现需要改额外文件 → 停下来，更新 Spec 的 Locks
```

### 检查命令

```bash
# 快速检查两个 Spec 是否有文件冲突
comm -12 <(grep "^- " specs/batch-101.md | sed 's/^- //' | sort) \
         <(grep "^- " specs/batch-102.md | sed 's/^- //' | sort)
```

## 为什么有效

- **声明式而非发现式**: 冲突在开工前就被发现，不是在合并时才暴露
- **最大化并行**: 只有真正冲突的任务才串行，其余全部并行
- **限制爆炸半径**: 执行者被约束在声明范围内，不会意外改到其他任务的文件
- **零运行时成本**: 纯文本检查，不需要锁服务或进程间通信

## 适用条件

- 多个 AI Agent 可以同时开工（有 Harness 或多 session 支持）
- 项目文件数 > 20（文件太少则并行收益低）
- 任务间有一定独立性（完全串行依赖的任务无需 Locks）
- Spec 驱动的工作流（每个任务有明确的 Spec 文件）

## 实战证据

VoiceOS 3.0 在协议 v5 引入 Locks 机制。B109（7 个 Part 的 Epic Batch）使用 Locks 声明后，实现了 **0 文件冲突**的并行执行。对比之前 B86-B90 期间多次出现"两个 Agent 改同一个文件"导致的 build 失败，Locks 从根本上消除了这类问题。
