# 案例研究: VoiceOS 3.0 — Harness 优化

> 2026-05-26 | 从 Agent Harness 研究到协作体系结构性升级

## 背景

VoiceOS 3.0 是一个 AI 创始人商业表达系统，前端 Next.js + Cloudflare Pages，后端 Cloudflare Workers + D1。经过 55 个 Batch 的开发后，协作体系暴露了三个结构性问题。

## 问题

1. **天工在脏状态上开工** — 缺少结构化启动确认，可能带着未提交改动或 build 错误开始新任务
2. **验证靠"肉眼看"** — Spec 的验证标准是文字描述，执行者不实际运行 bash 就标记完成
3. **大改动丢失风险** — 所有 Part 完成后才一次性 commit，中途崩溃全部丢失

## 解决方案

研究了 Agent Harness 概念后，植入三项结构性优化：

### 优化 1: Initializer Protocol
执行者启动时先跑 pwd → git status → build → 任务看板，输出结构化总结。

### 优化 2: Spec verify_cmd
每个任务新增可执行的 bash 验证命令，执行者和审核者都必须实际运行。

### 优化 3: 增量 commit
每 1-3 个任务提交一次 + build 验证，而不是全做完再提交。

## 效果

- 执行者启动即有上下文，减少"build 一直过不了"的死循环
- 验证从主观判断升级为客观的命令输出
- 出问题可以精准 revert，不影响其他改动

## 可迁移的实践

本案例提炼出的通用实践：
- `practices/harness-initializer.md`
- `practices/spec-verify-cmd.md`
- `practices/incremental-commit.md`
