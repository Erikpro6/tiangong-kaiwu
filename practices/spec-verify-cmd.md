# 实践: Spec verify_cmd — 可执行的验证命令

> 提炼自 VoiceOS 3.0 Spec 模板优化

## 问题

Spec 中的验证标准是文字描述（如"确保编译通过"），执行者可能不实际运行 bash 命令就标记完成，审核者也无法自动化验证。

## 方案

在每个任务（Part）中新增 `verify_cmd` 字段，包含可执行的 bash 命令：

```markdown
**verify_cmd**:
\```bash
# 类型检查
npx tsc --noEmit --pretty 2>&1 | head -20
# 构建检查
npx next build 2>&1 | tail -5
# 特定文件检查
grep -n "export function" src/lib/api.ts
\```
```

执行者和审核者都必须**实际运行**这些命令，看到输出通过才能标记完成。

## 为什么有效

- 从"肉眼看"升级为"机器验"
- 审核者可以批量运行 verify_cmd，快速定位问题
- 验证标准不再是主观判断，而是客观的命令输出

## 适用条件

- 有构建步骤的项目
- 需要多角色审核的工作流
- Spec 驱动的执行模式
