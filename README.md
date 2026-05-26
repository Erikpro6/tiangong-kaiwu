# 天工开物 · Tiangong Kaiwu

> **Production-proven multi-agent collaboration protocol.** Born from 276 commits, 109 batches, and 5 protocol versions in a real product.

**English** | **[中文](./README.zh-CN.md)**

[![Practices](https://img.shields.io/badge/practices-10-blue)](./practices)
[![Templates](https://img.shields.io/badge/templates-3-green)](./templates)
[![Protocol](https://img.shields.io/badge/protocol-v5-orange)](./cases/voiceos-harness.md)
[![License](https://img.shields.io/badge/license-MIT-black)](./LICENSE)

> *In 1637, Song Yingxing wrote 《天工开物》— an encyclopedia documenting how artisans of every craft transformed raw materials into finished works through division of labor and systematic process. 389 years later, the same principle powers AI agents collaborating to ship production software.*

---

## The Problem

You're using AI agents (Claude Code, Cursor, Copilot) to build software. You've tried:

- **Vibes coding** — fun for prototypes, chaos for anything real
- **One agent, one prompt** — works for small tasks, breaks at scale
- **Multi-agent without rules** — agents overwrite each other, skip tests, miss specs

The gap between "AI can write code" and "AI can ship a product" is **coordination**.

## What This Is

A battle-tested set of **practices, templates, and case studies** for coordinating multiple AI agents to build production software. Not a framework, not a library — a **protocol** you copy into your project.

```
You (human)
  ↓ intent
Commander Agent → writes Spec
  ↓ Spec
Executor Agent  → codes, builds, commits
  ↓ code
Reviewer Agent  → audits against Spec
  ↓ ✅/❌
Deployer       → ships it
```

### Core Principles

1. **Files are the only communication channel** — agents don't chat, they read/write files
2. **Humans are the final gate** — AI proposes, human approves
3. **Roles are strict, tasks are split** — when a task crosses role boundaries, split the task, not the rules
4. **Machine-verified, not eye-checked** — every task ships with executable verification commands

---

## Proven Results

From the [VoiceOS 3.0](./cases/voiceos-harness.md) project (the primary case study):

| Metric | Value |
|--------|-------|
| Batches tracked | 109, zero missed |
| Protocol versions | v1 → v5, self-evolving |
| First-pass approval rate | 33% → **100%** (after 5 evolutions) |
| Peak throughput | 22 batches in 36 hours |
| File conflicts in parallel execution | **0** (after Spec Locks) |
| Role boundary violations | **0** (after split mode) |

---

## Quick Start

### 1. Copy the templates

```bash
# Copy the main protocol file to your project root
cp templates/AGENTS-template.md YOUR_PROJECT/AGENTS.md

# Create required directories
mkdir -p YOUR_PROJECT/docs/{specs,reviews,optimizations}

# Copy templates
cp templates/spec-template.md YOUR_PROJECT/docs/specs/_TEMPLATE.md
cp templates/review-template.md YOUR_PROJECT/docs/reviews/_TEMPLATE.md
```

### 2. Choose your roles

You don't need all 6 roles. Start small:

| Scenario | Roles Needed |
|----------|-------------|
| Solo dev + AI | **Commander** + **Executor** (2 roles minimum) |
| 2 people (PM + Dev) | Commander + Executor + **Reviewer** |
| 3+ team | Full 6-role setup |

### 3. Create a progress tracker

Create a `docs/progress.md` with the emoji state machine:

```markdown
| Batch | Status | Content | Spec |
|-------|--------|---------|------|
| B1 | 🔴Not Started | Setup auth | specs/batch-1 |
| B2 | 🔴Not Started | Dashboard UI | specs/batch-2 |
```

Status flow: `🔴 → 🟡 → 🟢 → ✅ → 🔵` (or `❌` back to 🟡)

### 4. Start working

Tell your AI agent to read `AGENTS.md` and start. See the [Migration Guide](./MIGRATION-GUIDE.md) for detailed setup.

---

## Practices

### Foundation

| Practice | What It Solves |
|----------|---------------|
| [File-Driven Roles](./practices/file-driven-roles.md) | Agents coordinate through files, not conversations |
| [Emoji State Machine](./practices/emoji-state-machine.md) | One Markdown file tracks all task status — no Jira needed |
| [Harness Initializer](./practices/harness-initializer.md) | Structured startup: agents confirm clean state before working |

### Spec Engineering

| Practice | What It Solves |
|----------|---------------|
| [Spec Verify Commands](./practices/spec-verify-cmd.md) | Executable bash commands replace "I checked and it looks fine" |
| [Spec Locks](./practices/spec-locks.md) | Declare file ownership per task → zero merge conflicts in parallel |
| [Spec Weight Classes](./practices/spec-weight-classes.md) | Micro / Standard / Epic — right-size the spec to the task |

### Quality & Evolution

| Practice | What It Solves |
|----------|---------------|
| [Role Boundary Enforcement](./practices/role-boundary-enforcement.md) | Split tasks across roles, don't blur role boundaries |
| [Self-Evolving Protocol](./practices/self-evolving-protocol.md) | Every mistake becomes a rule — protocol improves itself |
| [Incremental Commit](./practices/incremental-commit.md) | Commit every 1-3 parts, not at the end — safer, more traceable |
| [Naming Conventions](./practices/naming-conventions.md) | One reference sheet for files, commits, statuses, and identifiers |

---

## How It Works

### The Lifecycle of a Task

```
  Commander writes Spec (with locks + verify_cmd)
         ↓
  Executor reads Spec → codes Part by Part
         ↓ (each Part)
  Executor runs verify_cmd → commits
         ↓ (all Parts done)
  Executor marks 🟢 Ready for Review
         ↓
  Reviewer audits code against Spec
         ↓
  ✅ Approved → Commander deploys → 🔵 Shipped
  ❌ Rejected → back to 🟡, Executor fixes
```

### The Self-Evolution Loop

```
  Something goes wrong
         ↓
  Commander logs: what happened → root cause → new rule
         ↓
  Protocol updated → version bumped (v4 → v5)
         ↓
  Same class of error never happens again
```

11 evolutions were recorded during VoiceOS 3.0 development. Each one permanently fixed a class of problem.

---

## What's Included

```
├── practices/          ← 10 reusable practices (copy what you need)
├── templates/          ← 3 ready-to-use templates
│   ├── AGENTS-template.md      ← Main protocol (copy to project root)
│   ├── spec-template.md        ← Task spec template
│   └── review-template.md      ← Code review template
├── cases/              ← Real-world case studies
│   └── voiceos-harness.md      ← 109-batch evolution story
├── MIGRATION-GUIDE.md  ← Step-by-step setup guide
└── README.md           ← You are here
```

---

## FAQ

**Do I need to use all 9 practices?**
No. Start with File-Driven Roles + Emoji State Machine (the foundation), then add practices as you hit problems. The [Migration Guide](./MIGRATION-GUIDE.md) has a minimal setup path.

**Does this only work with Claude Code?**
No. The protocol is agent-agnostic — it works with any AI coding agent that can read files and run shell commands (Claude Code, Cursor, Copilot CLI, etc.). The templates use Claude Code conventions but are easy to adapt.

**Is this a library or framework?**
Neither. It's a set of Markdown files you copy into your project. Zero dependencies, zero installation, zero lock-in.

**What if I'm a solo developer?**
The minimum viable setup is 2 roles: Commander (you + AI) and Executor (AI). The [Migration Guide](./MIGRATION-GUIDE.md) covers this scenario.

**How is this different from prompt engineering?**
Prompt engineering optimizes *one* agent's output. This optimizes *multiple* agents' collaboration. Different layer of the stack.

---

## Origin Story

All practices were extracted from [VoiceOS 3.0](https://voiceos-3-web.pages.dev), a production AI cognitive OS built entirely with multi-agent collaboration:

- **276 commits** by 6 specialized AI roles
- **109 batches** tracked with zero missed
- Protocol evolved from **v1 to v5** over 3 weeks
- First-pass approval rate improved from **33% to 100%**
- **Zero file conflicts** after Spec Locks were introduced

Read the full case study → [VoiceOS 3.0 Harness Evolution](./cases/voiceos-harness.md)

---

## Why "天工开物"?

**天工开物** (*Tiangong Kaiwu*), written by Song Yingxing in 1637, is China's first comprehensive encyclopedia of technology and craftsmanship. It documented how artisans across 18 industries — from metallurgy to shipbuilding to silk weaving — transformed raw materials into finished works through systematic process and division of labor.

The parallel is intentional: just as Song Yingxing observed that great works emerge not from individual genius but from **clear roles, systematic process, and accumulated craft knowledge**, this project proves that AI agents produce their best work under the same conditions.

天工 (*Tiangong*) literally means "heavenly craftsmanship" — work so refined it seems divinely inspired. 开物 (*Kaiwu*) means "to create things" — the act of bringing new artifacts into the world.

Together: **the art of making extraordinary things through disciplined collaboration.**

---

## License

MIT — use freely, attribution appreciated.
