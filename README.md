# AI 工作流实践

> 从真实项目中提炼的多角色 AI 协作工作流，可直接迁移到新项目。

## 这是什么

一套经过实战验证的 AI 多角色协作方法论：
- **6 个角色**定义清晰、边界分明
- **文档驱动**沟通（Spec / Review / Optimization）
- **状态流转看板**跟踪每个任务
- **自进化机制**持续优化协议

## 核心理念

1. **文档是唯一沟通载体** — 角色之间不靠对话，靠文件（Spec → Review → Optimization）
2. **人是最终决策关卡** — AI 提方案，人确认后才执行
3. **角色分离、职责单一** — 指挥的不编码，编码的不审核，审核的不改代码
4. **可验证 > 可读** — 每个任务带 verify_cmd，机器验而非肉眼看

## 目录结构

```
practices/        ← 可复用实践（每个实践一个 .md）
templates/        ← 可直接复制使用的模板
roles/            ← 角色定义（通用版，去掉了项目特异性）
cases/            ← 案例研究（来自真实项目）
```

## 快速开始

1. 复制 `templates/AGENTS-template.md` 到你的项目根目录，改名为 `AGENTS.md`
2. 根据项目需要选择角色（不需要全部 6 个）
3. 复制 `templates/spec-template.md` 到 `docs/specs/_TEMPLATE.md`
4. 开始工作

## 来源

所有实践均来自 [VoiceOS 3.0](https://voiceos-3-web.pages.dev) 项目的真实协作过程：
- 36 小时完成 22 个 Batch（Phase ABCD）
- 55 个 Batch 后第一次用户测试发现 12 个 Bug → 验证了审核流程的价值
- 持续优化协议 v1 → v4（Harness 优化版）
