# Naming Conventions

> One reference sheet for all file names, commit messages, status codes, and identifiers used in the protocol.

## Why

When multiple agents read and write files in the same repo, consistent naming prevents confusion, avoids collisions, and makes everything grep-able. Without it, you get `spec-1.md`, `Spec_1.md`, `batch1.md` for the same thing — and agents can't find what they need.

## File Naming

### Specs

All spec files live in `docs/specs/`:

| Type | Pattern | Examples |
|------|---------|----------|
| Numeric | `batch-{NN}.md` | `batch-41.md`, `batch-72.md` |
| Phase stage | `batch-{phase}{N}.md` | `batch-a1.md`, `batch-c6.md` |
| Phase sub-task | `batch-{phase}{letter}{N}.md` | `batch-ba1.md`, `batch-ba3.md` |
| Deploy | `batch-d{N}.md` | `batch-d1.md` |
| Fix | `batch-{NN}-fix{N}.md` | `batch-6b-fix1.md` |

Rules:
- Lowercase, hyphen-separated
- No mixed formats within the same Phase (don't mix `batch-a1` and `ba1`)
- Template file: `_TEMPLATE.md` (underscore-prefixed, agents copy from this)

### Reviews

| Type | Pattern |
|------|---------|
| Standard | `docs/reviews/batch-{N}.md` |
| Fix review | `docs/reviews/batch-{N}-fix{N}.md` |

### Audits

| Type | Pattern |
|------|---------|
| Ops audit | `docs/audits/yuanwang-audit-YYYY-MM-DD.md` |
| Tech debt | `docs/audits/tech-debt-YYYY-MM-DD.md` |
| Pre-launch | `docs/audits/pre-launch.md` |
| Retrospective | `docs/audits/retrospective-YYYY-MM-DD.md` |

### Optimization Proposals

Pattern: `docs/optimizations/YYYY-MM-DD-{topic}.md`

Example: `docs/optimizations/2026-05-20-spec-locks.md`

### Directory Structure

```
docs/
  progress.md              ← task board (single source of truth)
  specs/
    _TEMPLATE.md           ← spec template
    batch-{N}.md           ← spec files
  reviews/
    batch-{N}.md           ← review reports
  audits/
    YYYY-MM-DD-{type}.md   ← audit reports
  optimizations/
    YYYY-MM-DD-{topic}.md  ← optimization proposals
```

## Commit Messages

### Format

```
{type}: batch-{N} Part{X}[-{Y}] {description}
```

| Field | Values |
|-------|--------|
| `type` | `feat` (implementation), `chore` (status/metadata), `fix` (bug fix), `style` (visual only) |
| `batch-{N}` | Batch identifier from progress.md |
| `Part{X}` or `Part{X}-{Y}` | Single part or range |
| `description` | One-line summary |

### Examples

```
feat: batch-87 Part1 stt-adapter-and-waveform
feat: batch-87 Part2-3 tts-adapter-and-synthesis
chore: batch-87 🟢待审核
fix: batch-89 Part2 fix-sse-timeout-120s
style: batch-86 chat-input-visual-fix
```

### Commit Frequency

| Part count | Strategy |
|------------|----------|
| 1–3 | One commit after all parts |
| 4–6 | Two commits (first half + second half) |
| 7+ | Every 3 parts |

Each commit must pass build verification before proceeding.

## Batch Identifiers

In `progress.md`, batches use short IDs in the table:

| Format | Example | Used for |
|--------|---------|----------|
| `B{NN}` | `B1`, `B87` | General batches |
| `{phase}{N}` | `A1`, `C6` | Phase-grouped batches |
| Phase sub-task | `B-A1` | Sub-tasks within a phase |
| Deploy | `D1` | Deployment batches |

The Spec file path is always written without `.md` extension: `specs/batch-87`, not `specs/batch-87.md`.

## Emoji State Machine

Status flow for every batch:

```
🔴 → 🟡 → 🟢 → ✅ → 🔵
              ↑      ↓
              ← ← ❌
```

| Emoji | Status | Who sets it | When |
|-------|--------|-------------|------|
| 🔴 | Not Started | Commander | Planning phase |
| 🟡 | In Progress | Executor | Claiming the task |
| 🟢 | Ready for Review | Executor | All parts done + build pass |
| ✅ | Approved | Reviewer | Review passed |
| 🔵 | Deployed | Commander/Ops | Live in production |
| ❌ | Rejected | Reviewer | Review failed → back to 🟡 |

### Review Level (inside Spec)

| Emoji | Meaning |
|-------|---------|
| 🔴 | Must review — Reviewer audits code |
| 🟢 | Self-verified — Executor runs verify_cmd, no review needed |

### Priority (for tech debt / issues)

| Emoji | Priority |
|-------|----------|
| 🔴 | High — affects functionality |
| 🟡 | Medium — affects maintainability |
| 🟢 | Low — cleanup items |

## Spec Internal Structure

Every spec file follows this structure:

```markdown
# Spec: batch-{N}

## Meta
| Field | Value |
|-------|-------|
| Batch | {N} |
| Weight | Standard / Epic |
| Executor | {role} |
| Locks | {file1}, {file2} |

## Part {N}: {title}
- [ ] {description}
- **verify_cmd**: `{bash command}`

## Execution Progress
- [ ] Part 1: {title}
- [ ] Part 2: {title}
```

After completion, each part must record verify output:

```
[x] Part 1: {title} -- verify: {actual output}
```

## Evolution Log

Protocol evolution entries use sequential IDs:

| Field | Pattern | Example |
|-------|---------|----------|
| Entry ID | `E-{NNN}` | `E-001`, `E-011` |
| Protocol version | `v{N}` | `v1` through `v5` |

Version bump rules:
- **Minor** (v4 → v5): new rules, new fields, wording changes
- **Major**: new roles, process restructuring, architectural changes

## Role System

Roles are named after Chinese astronomical instruments and mythological figures — each name reflects the role's function:

| Name | Meaning | Role | Responsibility |
|------|---------|------|----------------|
| **北斗** (Beidou) | Big Dipper — the compass in the sky | Commander | Plans direction, writes Specs, manages progress, deploys |
| **天工** (Tiangong) | Divine Craftsman — heavenly maker | Executor | Reads Spec, codes Part by Part, builds, commits |
| **巡天** (Xuntian) | Sky Survey — scanning the heavens | Reviewer | Audits code against Spec, writes review reports |
| **神笔** (Shenbi) | Magic Brush — from the legend of Ma Liang | Visual Designer | CSS/styling only — never touches logic code |
| **小诸葛** (Xiaozhuge) | Little Zhuge Liang — the legendary strategist | Process Optimizer | Finds bottlenecks, improves protocol, extracts reusable practices |
| **远望** (Yuanwang) | Look Afar — deep observation | Ops / Audit | Type safety, build checks, production monitoring |

### Naming Philosophy

Just as ancient Chinese astronomers named constellations after their function (北斗 for navigation, 巡天 for surveying), each agent role is named after what it *does*:

- **北斗** (Big Dipper) always points north → Commander sets direction
- **天工** (divine craftsmanship) creates things → Executor builds
- **巡天** (sky survey) observes and records → Reviewer inspects
- **神笔** (magic brush) brings images to life → Designer makes it beautiful
- **小诸葛** (strategist) sees the battlefield → Optimizer improves the process
- **远望** (look afar) watches the horizon → Ops catches problems before they arrive

### Minimum Viable Setup

You don't need all 6 roles:

| Team Size | Roles | Setup |
|-----------|-------|-------|
| Solo dev + AI | **北斗** + **天工** | 2 roles, minimum viable |
| 2 people | 北斗 + 天工 + **巡天** | Add review gate |
| 3+ team | Full 6-role setup | Maximum coordination |

### Role Boundaries

Each role reads and writes specific file types:

| Role | Reads | Writes |
|------|-------|--------|
| 北斗 | reviews/, progress.md, optimizations/ | specs/, progress.md |
| 天工 | specs/ | source code, progress.md (status only) |
| 巡天 | specs/, source code | reviews/ |
| 神笔 | specs/ (visual tasks only) | globals.css, className values |
| 小诸葛 | progress.md, reviews/, AGENTS.md | optimizations/ |
| 远望 | source code, build output | audits/ |

When a task crosses role boundaries, split the task — don't blur the roles. See [Role Boundary Enforcement](./role-boundary-enforcement.md).

## How to Apply

1. **On project setup**: Create the directory structure above and copy templates
2. **Commander writes specs**: Follow `batch-{N}.md` naming, declare `locks`
3. **Executor commits**: Follow `feat: batch-N Part{X} description` format
4. **Reviewer writes reports**: Follow `reviews/batch-{N}.md` naming
5. **Everyone updates progress.md**: Use emoji status, no custom status strings

Consistency is mechanical — once the team (human + agents) follows these rules, everything is grep-able, sortable, and unambiguous.
