````md
---
name: commit-pattern
description: Use when committing code to apply standardized commit format with icons, issue references, AI flags, run quality checks before commit, and auto-push.
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
````

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
✨ (auth): [claude] add OAuth login support (#123)

🐛 (api): [cursor] fix token refresh issue (#145)

🔧 (auth): [copilot] simplify oauth flow (#120, #121)
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
| main      | ❌              |
| develop   | ✅              |
| feature/* | ✅              |
| fix/*     | ✅              |
| hotfix/*  | ✅              |
| release/* | ✅              |

---

# Output Contract

Commit created + quality checks executed + auto-push + branch report.

---

# Quality Bar

* Required format:

  ```text
  <icon> (<scope>): [<ai-flag>] <message> (#123)
  ```

* Message in English

* Imperative mood

* ≤72 characters whenever possible

* Checks must pass

* Never commit broken code

* Auto-push after commit

* Report branch at the end

---

# Format

## Non-main branches

```text
<icon> (<scope>): [<ai-flag>] <message> (#123)
```

Examples:

```text
✨ (auth): [claude] add OAuth login support (#123)

🐛 (api): [cursor] fix token refresh issue (#145)

🔧 (skill): [copilot] update commit workflow rules (#120, #121)
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
| ✨    | feat     |
| 🐛   | fix      |
| 📚   | docs     |
| 💅   | style    |
| 🔧   | refactor |
| ⚡    | perf     |
| ✅    | test     |
| 📦   | build    |
| 🔨   | ci       |
| 🧹   | chore    |
| ⏪    | revert   |

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

| AI Agent | Flag         |
| -------- | ------------ |
| Codebuff | `[codebuff]` |
| Claude   | `[claude]`   |
| Cursor   | `[cursor]`   |
| Copilot  | `[copilot]`  |

Human commits omit the flag.

---

# Correct Examples

```text
✨ (auth): [claude] add OAuth login support (#123)

🐛 (api): [cursor] fix timeout issue (#145)

📚 (docs): [copilot] update installation guide (#110)

🔧 (skill): [claude] improve commit workflow (#132)
```

---

# Wrong Examples

```text
feat: add login

✨ add login

✨ (feat): add login

✨ (auth): [claude] add login
```

(last example is invalid because issue reference is missing outside `main`)

---

# Message Rules

1. English only.
2. Imperative mood.
3. One logical change per commit.
4. AI commits must include flags.
5. Non-main branches must include issue references.
6. Main branch follows release workflow.

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

* Fix before commit.
* Never commit broken code.
* Report errors.

---

# Auto Push

```bash
git push origin <current-branch>
```

Always push after commit.

---

# Workflow

1. `git status`
2. Preserve unrelated changes.
3. Modify only required files.
4. Run checks.
5. Stage only related files.
6. Create commit.
7. Push.
8. Report branch.

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
8. Push main and tags.
9. Return to develop.

---

# Version Rules

| Commit          | Version |
| --------------- | ------- |
| 💥 🔥           | major   |
| ✨               | minor   |
| everything else | patch   |

---

# Release Commit Format

```text
🚀 Release v1.2.0

✨ New features
• (auth): [claude] add OAuth login (#123)

🐛 Bug fixes
• (api): [cursor] fix timeout issue (#145)

🔧 Refactor
• (store): [copilot] simplify state management (#130)
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
✨ (auth): [claude] add OAuth login support (#123)
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
✨ (auth): [claude] add OAuth login support (#123)
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
🔧 (auth): [claude] simplify oauth flow (#120, #121)
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