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

## How to Apply

1. **On project setup**: Create the directory structure above and copy templates
2. **Commander writes specs**: Follow `batch-{N}.md` naming, declare `locks`
3. **Executor commits**: Follow `feat: batch-N Part{X} description` format
4. **Reviewer writes reports**: Follow `reviews/batch-{N}.md` naming
5. **Everyone updates progress.md**: Use emoji status, no custom status strings

Consistency is mechanical — once the team (human + agents) follows these rules, everything is grep-able, sortable, and unambiguous.
