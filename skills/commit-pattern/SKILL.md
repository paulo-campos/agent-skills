---
name: commit-pattern
description: Use when committing code to apply standardized commit format with icons, issue references, AI flags, run quality checks before commit.
---

# Commit Pattern

Standardized commit workflow with icon-based conventional commits, GitHub issue references, AI identifier flags, visual formatting, quality checks, and release automation.

## Goal

Standardize commits, ensure traceability between Issues → Commits → Releases, include AI identifier flags, execute quality checks before commit, and automate releases.

---

# Intake

Preferred input:

```text
Issue: #123

Implement OAuth login.
```

Or:

```text
Issues:
- #123
- #124

Refactor authentication flow.
```

---

# Issue Rules

## Non-main branches

For every branch except `main`, commits SHOULD be associated with one or more GitHub Issues.

Examples:

```text
develop
feature/*
fix/*
hotfix/*
release/*
```

### Commit format

```text
✨ (auth): [{ai-flag}] add OAuth login support #123

🐛 (api): [{ai-flag}] fix token refresh issue #145

🔧 (auth): [{ai-flag}] simplify oauth flow #120, #121
```

---

## Missing Issue Handling

If the user requests code changes but does not provide issue numbers:

### The AI MUST:

1. Ask for the related issue number.
2. Stop before committing.
3. Never invent issue references.
4. Never create commits without issue references.

Example:

```text
User:
Implement OAuth login.

Assistant:
Please provide the related GitHub issue number before committing.

Example:
Issue: #123
```

---

## Main Branch Exception

Issue references are NOT required on `main`.

The `main` branch follows the release workflow and uses release commits:

```text
🚀 Release v1.4.0
```

---

# Branch Rules

| Branch    | Require Issues |
| --------- | -------------- |
| main      | ❌             |
| develop   | ✅             |
| feature/* | ✅             |
| fix/*     | ✅             |
| hotfix/*  | ✅             |
| release/* | ✅             |

---

# Output Contract

Commit created + quality checks executed + branch report. (Commits stay LOCAL unless user explicitly requests push.)

---

# Quality Bar

- Required format:

  ```text
  <icon> (<scope>): [{ai-flag}] <message> #123
  ```

- Message in English

- Imperative mood

- ≤72 characters whenever possible

- Checks must pass

- Never commit broken code

- Never use `--no-verify` in commits or pushes

- Report branch at the end

---

# Format

## Non-main branches

```text
<icon> (<scope>): [{ai-flag}] <message> #123
```

Examples:

```text
✨ (auth): [{ai-flag}] add OAuth login support #123

🐛 (api): [{ai-flag}] fix token refresh issue #145

🔧 (skill): [{ai-flag}] update commit workflow rules #120, #121
```

---

## Main branch

Release commits only:

```text
🚀 Release v1.4.0
```

---

# Icon Reference

| Icon | Type     |
| ---- | -------- |
| ✨   | feat     |
| 🐛   | fix      |
| 📚   | docs     |
| 💅   | style    |
| 🔧   | refactor |
| ⚡   | perf     |
| ✅   | test     |
| 📦   | build    |
| 🔨   | ci       |
| 🧹   | chore    |
| ⏪   | revert   |

---

# Common Scopes

| Scope  | Usage             |
| ------ | ----------------- |
| auth   | Authentication    |
| api    | Backend endpoints |
| store  | State management  |
| ui     | Components/layout |
| db     | Database          |
| skill  | Skills            |
| docs   | Documentation     |
| config | Config files      |
| ci     | CI/CD             |
| deps   | Dependencies      |

---

# AI Identifier Flags

Each AI agent MUST use its own name as the flag. The flag is always lowercase, inside square brackets.

**Examples:**
| AI Agent | Flag |
| -------- | ---- |
| Codebuff | `[codebuff]` |
| Claude | `[claude]` |
| Cursor | `[cursor]` |
| Copilot | `[copilot]` |
| Opencode | `[opencode]` |

Human commits omit the flag.

**Rule:** Replace `{ai-flag}` with your own agent name in lowercase (e.g., if you are Opencode, use `[opencode]`).

---

# AI Flag Replacement

The placeholder `{ai-flag}` MUST be replaced with your own agent name in lowercase.

**Rules:**
- Replace `{ai-flag}` with `[your-agent-name]` (e.g., `[opencode]`, `[claude]`)
- Do NOT include the curly braces `{}` in the output
- Do NOT add angle brackets `< >` around the flag
- The square brackets `[]` ARE part of the final format
- Never commit with `{ai-flag}` literal — always replace it

**Correct:**
```text
✨ (auth): [opencode] add login #123
✨ (auth): [claude] add login #123
✨ (auth): [codebuff] add login #123
```

**Wrong:**
```text
✨ (auth): [{ai-flag}] add login #123    ← placeholder not replaced
✨ (auth): [<opencode>] add login #123   ← angle brackets added incorrectly
```

---

# Correct Examples

```text
✨ (auth): [opencode] add OAuth login support #123

🐛 (api): [claude] fix timeout issue #145

📚 (docs): [codebuff] update installation guide #110

🔧 (skill): [cursor] improve commit workflow #132
```

---

# Wrong Examples

```text
feat: add login

✨ add login

✨ (feat): add login

✨ (auth): [{ai-flag}] add login
```

(last example is invalid because issue reference is missing outside `main`)

### Wrong Examples (releases)

```text
git commit -m '🚀 Release v1.2.0\n\n✨ New features\n• (auth)...'
```

---

# Message Rules

1. English only.
2. Imperative mood.
3. One logical change per commit.
4. AI commits must include flags.
5. Non-main branches must include issue references.
6. Main branch follows release workflow.
7. Release commits on `main` MUST use multiple `-m` flags to include body.

---

# Pre-Commit Checks

Always run:

```bash
npm run lint
npm run typecheck
npm test
npx prettier --write .
```

If checks fail:

- Fix before commit.
- Never commit broken code.
- Report errors.

---

# ⚠️ CRITICAL: Push and Merge Rules

> **NEVER execute `git push` or `git merge` unless the user EXPLICITLY requests it in the same message.**

### Absolute Rules

1. **NEVER push after commit** — commits stay LOCAL by default
2. **NEVER merge branches** — unless user explicitly says "merge"
3. **NEVER assume permission** — even if user allowed it before, each request is independent
4. **ONLY exception: `#release` command** — this is the ONLY time push/merge happens automatically

### What User Must Say

| Action | Required User Input |
| ------ | ------------------- |
| Push   | "push", "enviar", "push it" |
| Merge  | "merge", "juntar", "merge branch X into Y" |
| Release| `#release` |

### Prohibited Actions (NEVER DO)

- `git push` — unless user explicitly requests
- `git push origin <branch>` — unless user explicitly requests
- `git merge` — unless user explicitly requests
- `git merge <branch>` — unless user explicitly requests
- `git checkout main && git merge develop` — ONLY via `#release`

### Important

- Each conversation is independent — permission granted once does NOT carry over
- After `#release`, do NOT assume you can push again — wait for explicit request
- If unsure, ASK before pushing or merging
- Report what you did, never assume what you can do

---

# Workflow

1. `git status`
2. Preserve unrelated changes.
3. Modify only required files.
4. Run checks.
5. Stage only related files.
6. Create commit.
7. **STOP** — do NOT push unless explicitly asked.
8. Report branch and that commit is LOCAL only.

---

# Release Workflow (`#release`)

`#release` executes:

1. Determine version automatically.
2. Ensure clean `develop`.
3. Merge `develop → main`.
4. Generate changelog.
5. Update package version.
6. Create release commit.
7. Create tag.
8. Push `main` and tags.
9. Return to develop: `git checkout develop`

---

# Version Rules

| Commit          | Version |
| --------------- | ------- |
| 💥 🔥           | major   |
| ✨              | minor   |
| everything else | patch   |

---

# Release Commit — Git Command
 
ALWAYS use multiple `-m` flags for release commits on `main`. A single `-m` only
creates the subject line and discards the body.

**Note:** `\n` does NOT create line breaks in git commit messages. Each `-m` flag adds a separate paragraph.
 
**Correct (on `main`):**
```bash
git commit -m '🚀 Release v1.2.0' \
  -m '✨ New features' \
  -m '• (auth): [opencode] add OAuth login #123' \
  -m '🐛 Bug fixes' \
  -m '• (api): [opencode] fix timeout #145'
```

**Wrong:**
```bash
git commit -m '🚀 Release v1.2.0\n\n✨ New features\n• (auth)...'
# \n does NOT create line breaks in git commit messages
```

Each `-m` adds a separate paragraph separated by a blank line.

---

# Release Commit Format

```text
🚀 Release v1.2.0

✨ New features
• (auth): [{ai-flag}] add OAuth login #123

🐛 Bug fixes
• (api): [{ai-flag}] fix timeout issue #145

🔧 Refactor
• (store): [{ai-flag}] simplify state management #130
```

Issue references are optional inside release changelogs.

---

# Full Traceability Flow

```text
GitHub Issue
      ↓
Development Request
      ↓
Branch
      ↓
Commit
      ↓
Release
```

Example:

```text
Issue #123
      ↓
Implement OAuth login
      ↓
feature/oauth-login
      ↓
✨ (auth): [{ai-flag}] add OAuth login support #123
      ↓
🚀 Release v1.4.0
```

---

# Examples

## User Input

```text
Issue: #123

Implement OAuth login.
```

## Generated Commit

```text
✨ (auth): [{ai-flag}] add OAuth login support #123
```

---

## Multiple Issues

```text
Issues:
- #120
- #121
```

↓

```text
🔧 (auth): [{ai-flag}] simplify oauth flow #120, #121
```

---

## Missing Issue

```text
User:
Implement OAuth login.
```

↓

```text
Assistant:

Please provide the related GitHub issue number before committing.

Example:
Issue: #123
```
