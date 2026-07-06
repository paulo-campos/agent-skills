# Commit Pattern — Detailed Documentation

Standardized commit workflow with icon-based conventional commits, AI identifier flags, visual formatting, and quality checks.

## Table of Contents

- [Overview](#overview)
- [Commit Format](#commit-format)
- [Icon Reference](#icon-reference)
- [Common Scopes](#common-scopes)
- [AI Identifier Flags](#ai-identifier-flags)
- [Message Rules](#message-rules)
- [Branch Strategy](#branch-strategy)
- [Pre-Commit Checks](#pre-commit-checks)
- [Auto-Push](#auto-push)
- [Release Workflow](#release-workflow)
- [Examples](#examples)
- [Workflow](#workflow)

---

## Overview

This skill ensures consistent commit messages across projects and AI agents. Every commit follows a structured format that includes:

- **Icon** — Visual indicator of change type
- **Scope** — Area of codebase affected
- **AI Flag** — Identifier of which AI agent made the commit
- **Message** — Description in English, imperative mood

---

## Commit Format

```
<icon> (<scope>): [<ai-flag>] <message in English>
```

| Component | Description | Example |
|-----------|-------------|---------|
| `icon` | Emoji representing commit type | `✨` |
| `scope` | Area of codebase changed | `auth`, `api`, `ui` |
| `ai-flag` | AI agent identifier | `[codebuff]`, `[claude]` |
| `message` | Description in English | `add OAuth login` |

**Full example:**
```
✨ (auth): [codebuff] add login with Google
```

---

## Icon Reference

| Icon | Type | Description |
|------|------|-------------|
| ✨ | feat | New feature |
| 🐛 | fix | Bug fix |
| 📚 | docs | Documentation only |
| 💅 | style | Code style (formatting, semicolons, etc.) |
| 🔧 | refactor | Code change that neither fixes a bug nor adds a feature |
| ⚡ | perf | Performance improvement |
| ✅ | test | Adding or correcting tests |
| 📦 | build | Build system or external dependencies |
| 🔨 | ci | CI configuration files and scripts |
| 🧹 | chore | Other changes that don't modify src or test |
| ⏪ | revert | Reverts a previous commit |

---

## Common Scopes

| Scope | When to use |
|-------|-------------|
| `auth` | Authentication, login, signup, OAuth |
| `api` | Backend endpoints, API routes |
| `store` | State management, store actions |
| `ui` | UI components, layouts, styling |
| `db` | Database, migrations, RLS policies |
| `skill` | Skill files (.agents/skills/) |
| `docs` | Documentation files (README, .md) |
| `config` | Configuration files (tsconfig, vite, etc.) |
| `ci` | CI/CD workflows, GitHub Actions |
| `deps` | Package dependencies |

Use a descriptive scope when none of the above fit. Keep it short (1-2 words).

---

## AI Identifier Flags

Every commit made by an AI agent MUST include its identifier flag in square brackets.

| AI Agent | Flag |
|----------|------|
| Codebuff (Buffy) | `[codebuff]` |
| Claude | `[claude]` |
| Cursor | `[cursor]` |
| Copilot | `[copilot]` |
| Other / Human | _(omit flag)_ |

> **Rule:** Human commits do NOT include the flag. Only AI-generated commits include it.

---

## Message Rules

1. **Always in English** — no exceptions
2. **Imperative mood** — "add", not "added" or "adds"
3. **First line ≤ 72 characters** (including icon, scope, and flag)
4. **One logical change per commit** — don't mix unrelated changes
5. **AI commits must include flag** — `[codebuff]`, `[claude]`, `[cursor]`, `[copilot]`
6. **Human commits omit the flag** — no brackets
7. **Reference issues** in the footer when applicable

### Correct Format

```
✨ (auth): [codebuff] add login with Google
🐛 (api): [codebuff] solve timeout on user endpoint
🔧 (store): [codebuff] simplify state management
📚 (docs): [claude] update installation guide
✨ (skill): [codebuff] add commit-pattern skill
```

### Wrong Format (NEVER USE)

```
feat: add login with Google                          <- missing icon, scope, and flag
fix: solve timeout on user endpoint                  <- missing icon, scope, and flag
✨ add login with Google                              <- missing scope and flag
✨ (feat): add login with Google                      <- scope should be area, not type
✨ [Opencode AI] add login with Google               <- deprecated AI flag format
✨ (feat): [codebuff] add login with Google (extra)   <- extra text after flag
```

---

## Branch Strategy

- **Default branch**: If the user does not specify a branch, assume `develop`
- Before starting any change, check the current branch with `git branch --show-current`
- If no branch is specified and the current branch is **not** `develop`, switch to `develop` before making changes (`git checkout develop`)
- If the user specifies a branch, work on that branch
- **Never create new branches** for changes
- At the end, report which branch the commit was pushed to

---

## Pre-Commit Checks

**ALWAYS** run these checks before committing:

```bash
# 1. Check for lint errors
npm run lint

# 2. Check for type errors
npm run typecheck

# 3. Run tests (if available)
npm test

# 4. Format code with Prettier (if configured)
npx prettier --write .
```

**If checks fail:**

- Fix the errors before committing
- Never commit broken code
- If the fix is too large, log `// TODO(refactor):` and report at the end

---

## Auto-Push

After every commit, automatically push to the current branch:

```bash
git push origin <current-branch>
```

**Do not** ask for confirmation. Always push after commit.

---

## Release Workflow

### `#release` Command

When the user types `#release`, execute the **full release cycle** automatically:

1. **Determine version** — Ask the user which version type (major/minor/patch) or read the current version from the latest tag
2. **Check branch** — Ensure current branch is `develop` and working tree is clean
3. **Merge develop → main**
4. **Generate changelog** from commits since last tag
5. **Create release commit** with changelog
6. **Tag** with `v<version>`
7. **Push** main + tag
8. **Switch back** to develop

### Automated Release Steps

```bash
# 1. Determine next version
CURRENT=$(git describe --tags --abbrev=0 2>/dev/null || echo "v0.0.0")
# Ask user: major, minor, or patch

# 2. Ensure clean working tree on develop
git checkout develop
git pull origin develop

# 3. Merge into main
git checkout main
git merge develop --no-ff -m "Merge develop into main for release"

# 4. Generate changelog from commits since last tag
LOG=$(git log $CURRENT..HEAD --pretty=format:"%s" --no-merges)
# Parse into categories (✨ feat, 🐛 fix, 🔧 refactor, 📚 docs, 🧹 chore)

# 5. Create release commit
git commit --allow-empty -m "🚀 Release v<version>

<changelog grouped by type>
"

# 6. Tag
git tag -a v<version> -m "Release v<version>"

# 7. Push
git push origin main
git push origin v<version>

# 8. Switch back to develop
git checkout develop
```

### Changelog Format

The release commit message MUST follow this structure:

```
🚀 Release v<version>

✨ New features
- Description of feature (from commit messages)

🐛 Bug fixes
- Description of fix

🔧 Refactor
- Description of refactor

📚 Documentation
- Description of docs change

🧹 Maintenance
- Description of chore
```

### Versioning Rules

| Version | When to use | Example |
|---------|-------------|---------|
| **Major** | Breaking API changes | `1.0.0` → `2.0.0` |
| **Minor** | New features (backwards compatible) | `1.0.0` → `1.1.0` |
| **Patch** | Bug fixes (backwards compatible) | `1.0.0` → `1.0.1` |

---

## Examples

### Feature Addition (AI commit)

```bash
git add src/components/Header.tsx
git commit -m "✨ (ui): [codebuff] add user avatar display"
git push origin develop
```

### Bug Fix (AI commit)

```bash
git add src/services/api.ts
git commit -m "🐛 (api): [codebuff] solve timeout on user endpoint"
git push origin develop
```

### Refactor (AI commit)

```bash
git add src/hooks/useAuth.ts src/utils/auth.ts
git commit -m "🔧 (auth): [codebuff] simplify authentication flow"
git push origin develop
```

### Skill Addition (AI commit)

```bash
git add .agents/skills/vercel-deploy/SKILL.md
git commit -m "✨ (skill): [codebuff] add vercel-deploy skill"
git push origin develop
```

### Human Commit (no flag)

```bash
git add src/styles.css
git commit -m "💅 (ui): update button border radius"
git push origin develop
```

---

## Workflow

1. **Check branch** — Run `git branch --show-current`
   - If no branch was specified by the user and current branch is **not** `develop`, switch to `develop`
   - If the user specified a branch, switch to that branch
2. Run `git status` before making changes
3. Preserve existing changes not made by you
4. Edit only the necessary files
5. Run build/lint/test checks
6. Stage only files related to the change
7. Create commit with proper message format (including AI flag if applicable)
8. Push to current branch
9. Report completion status
