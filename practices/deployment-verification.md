# 实践: 部署验证

> 来源: VoiceOS 3.0 — 两次"改了没部署"事故（进化日志 #7, #12）

## 问题

Agent 完成了代码、通过了 build、甚至提交了 commit。但用户看到的是旧版本。中间缺少了最关键的一步：**部署到线上并验证**。

这种问题不是偶然——当协议没有把"部署"定义为显式步骤时，Agent 会在 build 通过后认为任务完成。

## 方案

### 部署验证四步法

```
Step 1: Build
  rm -rf {build_output} && {build_command}
  → 必须是 clean build（清缓存再构建）

Step 2: Post-build（如需要）
  {postbuild_command}
  → 静态路由修复、资源搬运等

Step 3: Deploy
  {deploy_command}
  → 推送到生产环境

Step 4: 验证（不可跳过）
  curl -s -o /dev/null -w "%{http_code}" {FRONTEND_URL}  → 期望 200
  curl -s {API_BASE}/health | head -1                    → 期望 {"status":"ok"...}
  → 至少验证 1 个页面 + 1 个 API 端点

Step 5: 告知用户
  "已部署到线上，请刷新浏览器测试"
  → 明确说"已上线"，不要说"已完成"
```

### 部署检查清单

每个项目应在 AGENTS.md 中声明部署命令：

```markdown
## 部署配置
| 步骤 | 命令 |
|------|------|
| Clean Build | `rm -rf out .next && npx next build` |
| Post-build | `node postbuild.mjs` |
| Deploy | `npx wrangler pages deploy out --project-name xxx` |
| 验证 | `curl -s -o /dev/null -w "%{http_code}" https://xxx.pages.dev` |
```

### 禁止事项

| 禁止 | 原因 |
|------|------|
| build 通过就说"完成" | build ≠ 部署 |
| 假设 CI/CD 自动部署 | 除非确认配置并验证过 |
| 部署后不验证 | 可能部署失败但 Agent 不知道 |
| 只 build 不部署然后让用户测试 | 浪费用户时间 |

### 部署节奏分层

| 优先级 | 部署策略 | SLA |
|--------|----------|-----|
| P0 安全/稳定性修复 | 立即部署（30 分钟内） | 指挥者手动执行 |
| P1 功能修复 | 当天部署 | 指挥者或执行者 |
| P2+ 新功能 | 按 Phase 批量部署 | 指挥者规划 |

### 多服务部署

当项目有多个服务（前端 + 后端 + 数据库）时：

```
1. 数据库 migration（如有）→ 先执行
2. 后端部署 → 等健康检查通过
3. 前端部署 → 最后执行
4. 端到端验证 → curl 前端页面 + 后端 API
```

顺序不能反——前端先部署但后端没准备好 = 用户看到错误。

## 为什么有效

- **显式步骤 > 隐式假设**：协议把"部署"和"验证"写成了不可跳过的步骤
- **curl 验证是客观的**：不是"应该好了"，而是"HTTP 200 + API 返回正确"
- **告知用户"已上线"**：用词从"完成"改为"已上线"，避免误解

## 适用条件

- 有部署环节的项目（不是纯本地开发）
- 多角色协作（执行者 build，指挥者部署）
- 用户在手机/浏览器上测试

## 与其他实践的关系

- 配合 [执行安全护栏](./execution-safety.md)（部署是破坏性操作，需确认）
- 配合 [自动化质量门禁](./automated-quality-gates.md)（门禁通过后才部署）
- 配合 [运维审计节奏](./ops-audit-cadence.md)（部署后远望验证线上状态）
