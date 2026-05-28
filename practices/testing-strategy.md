# 实践: 测试策略

> 来源: VoiceOS 3.0 — 测试覆盖率 1.5%，远望审计发现大量可被测试捕获的问题

## 问题

协议有 verify_cmd（构建验证），有质量门禁（L0-L4），但 276 个 commit 中 0 个测试 commit。结果是：
- 双重解包 bug（`apiFetch` 已解包再 `.data`）在生产环境丢失数据
- 50 个空 catch 块静默吞错
- 23 个 `as any` 类型断言绕过类型检查

这些问题如果有基础测试，在开发期就能捕获。但没有测试 = 每次改代码都是盲飞。

## 方案

### 测试金字塔在协议中的落地

```
        /  E2E  \          ← L3: Playwright/Cypress（Epic 级别 Spec 必须）
       / 集成测试 \         ← L2: API 调用 + 数据流验证
      /  单元测试  \        ← L1: 函数/组件级别（Standard 级别建议）
     / 静态检查     \       ← L0: tsc --noEmit + lint（所有 Spec 必须）
```

### 新增代码的测试要求

| 改动类型 | 测试要求 | 放在哪里 |
|----------|---------|---------|
| 新 API 端点 | ≥1 个测试（正常路径 + 错误路径） | `__tests__/{module}.test.ts` |
| 核心组件（认证、数据流） | ≥1 个测试 | `__tests__/{component}.test.ts` |
| 工具函数/数据转换 | ≥1 个测试 | `__tests__/{lib}.test.ts` |
| 纯 UI 样式改动 | 不需要 | — |
| Bug 修复 | 回归测试（防止再犯） | `__tests__/regression-{issue}.test.ts` |

### Spec 中的测试声明

Standard 级别（建议）和 Epic 级别（必须）的 Spec 应包含测试 Part：

```markdown
### Part N: 测试

**测试文件**: `__tests__/auth.test.ts`
**覆盖**:
- 正常登录 → 返回 token
- 错误密码 → 返回 401
- Token 过期 → 跳转登录

**verify_cmd**: `npx vitest run __tests__/auth.test.ts`
```

### verify_cmd 升级

当项目有测试文件时，verify_cmd 从纯 build 升级：

| 级别 | verify_cmd | 说明 |
|------|-----------|------|
| 无测试文件 | `next build` | 仅构建验证 |
| 有相关测试 | `next build && vitest run --related` | 构建 + 相关测试 |
| Epic 级别 | `next build && vitest run` | 构建 + 全量测试 |

### 测试文件命名

```
src/
  __tests__/
    api-fetch.test.ts       ← 测试 src/lib/api.ts
    auth.test.ts            ← 测试 src/lib/auth.ts
    swr-fetcher.test.ts     ← 测试 src/lib/swr-fetcher.ts
    regression-404.test.ts  ← 回归测试：修复 #404 的问题
```

### 覆盖率策略

- **新增代码** ≥ 50%（不追溯旧代码）
- **Bug 修复**必须有回归测试
- 不追求 100% 覆盖率——测试关键路径，不测 setter/getter

### 渐进式引入

不要求一次性补全所有测试。引入策略：

```
Phase 1: 新 Spec 必须包含测试（新增代码）
Phase 2: Bug 修复必须加回归测试
Phase 3: 远望审计时标注"缺少测试"的高风险模块
Phase 4: 对高风险模块补写测试
```

## 为什么有效

- **测试是可重复的验证**：手动验证一次，测试验证无限次
- **回归测试防止反复**：Bug 修一次，测试保证不会再出现
- **测试即文档**：`auth.test.ts` 告诉你认证系统该怎么用
- **与 verify_cmd 结合**：测试从"建议"变成"门禁"

## 适用条件

- 项目有测试框架（vitest、jest 等）
- 多角色协作（执行者写代码，测试让审核者有客观标准）
- 持续迭代（回归风险高，测试防止旧功能被新改动破坏）

## 与其他实践的关系

- 配合 [Spec 验证命令](./spec-verify-cmd.md)（verify_cmd 升级为包含测试）
- 配合 [自动化质量门禁](./automated-quality-gates.md)（L2 级别就是单元测试）
- 配合 [运维审计节奏](./ops-audit-cadence.md)（远望检查 vitest 通过率）
