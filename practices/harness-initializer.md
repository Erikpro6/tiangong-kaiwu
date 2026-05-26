# 实践: Harness Initializer — 结构化启动协议

> 提炼自 VoiceOS 3.0 天工模式

## 问题

AI Agent 启动时缺少结构化的上下文确认，可能带着脏工作区或 build 错误开工，导致后续改动全部浪费。

## 方案

收到执行指令后，先完成初始化检查：

```
1. 确认工作目录: pwd
2. 检查 Git 状态: git log --oneline -3 && git status --short
   - 有未提交的改动 → 汇报，等指示
3. 检查 Build 状态: next build（或项目对应的构建命令）
4. 读取任务看板: progress.md（或你项目的任务文件）
5. 输出结构化总结
```

## 为什么有效

- 避免在脏状态上堆叠改动（前一个角色的改动没 commit 就开始改）
- 提前发现 build 错误（不等到最后才发现）
- 结构化总结让用户一眼看清当前状态

## 适用条件

- 多角色协作（不同角色先后改动同一代码库）
- 项目有构建步骤（next build / cargo build / tsc 等）
- 有任务看板文件（progress.md / TODO.md 等）
