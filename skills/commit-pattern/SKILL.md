---
name: commit-pattern
description: Use ao commitar para aplicar formato padronizado com ícones, scope, flags de IA, rodar verificações de qualidade antes do commit e fazer push automático.
---

# Commit Pattern

Standardized commit workflow with icon-based conventional commits, AI identifier flag, visual formatting, and quality checks.

## Goal

Padronizar commits com formato icon-based, incluir flag de identificação de IA, executar quality checks antes de commit, e fazer push automático.

## Intake

Scope e mensagem do commit. A skill detecta o tipo automaticamente pelo conteúdo.

## Output Contract

Commit criado + quality checks executados + push automático + relatório do branch.

## Quality Bar

- Formato obrigatório: `<icon> (<scope>): [<ai-flag>] <message>`
- Mensagem em inglês, imperative mood, ≤72 caracteres
- Checks devem passar (lint, typecheck, test)
- Nunca commitar código quebrado
- Auto-push após commit
- Reportar branch ao final
- Se checks falharem, reportar erros e não commitar até resolver

## Format

```
<icon> (<scope>): [<ai-flag>] <message in English>
```

- **icon**: Emoji representing the commit type (see Icon Reference below)
- **scope**: Area of the codebase that changed (e.g., `auth`, `api`, `store`, `ui`, `skill`, `docs`, `config`)
- **ai-flag**: Identifier of the AI agent making the commit (see table below)
- **message**: Description in English, imperative mood, concise

> **Important:** The icon already conveys the TYPE of change (feat, fix, refactor, etc.).
> The scope describes WHAT part of the codebase was affected.
> For example: `✨ (auth): [codebuff] add OAuth login` — the icon ✨ means "new feature", the scope `auth` means the authentication module was changed.

### Icon Reference

| Icon | Type       | Description                                             |
| ---- | ---------- | ------------------------------------------------------- |
| ✨   | feat       | New feature                                             |
| 🐛   | fix        | Bug fix                                                 |
| 📚   | docs       | Documentation only                                      |
| 💅   | style      | Code style (formatting, semicolons, etc.)               |
| 🔧   | refactor   | Code change that neither fixes a bug nor adds a feature |
| ⚡   | perf       | Performance improvement                                 |
| ✅   | test       | Adding or correcting tests                              |
| 📦   | build      | Build system or external dependencies                   |
| 🔨   | ci         | CI configuration files and scripts                      |
| 🧹   | chore      | Other changes that don't modify src or test             |
| ⏪   | revert     | Reverts a previous commit                               |

### Common Scopes

| Scope | When to use |
| ----- | ----------- |
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

Use a descriptive scope when none of the above fit. The scope should be short (1-2 words).

### AI Identifier Flags

Every commit made by an AI agent MUST include its identifier flag in square brackets.

| AI Agent         | Flag          |
| ---------------- | ------------- |
| Codebuff (Buffy) | `[codebuff]`  |
| Claude           | `[claude]`    |
| Cursor           | `[cursor]`    |
| Copilot          | `[copilot]`   |
| Other / Human    | _(omit flag)_ |

> **Rule:** Human commits do NOT include the flag. Only AI-generated commits include it.

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

## Message Rules

1. **Always in English** — no exceptions
2. **Imperative mood** — "add", not "added" or "adds"
3. **First line ≤ 72 characters** (including icon, scope, and flag)
4. **One logical change per commit** — don't mix unrelated changes
5. **AI commits must include flag** — `[codebuff]`, `[claude]`, `[cursor]`, `[copilot]`
6. **Human commits omit the flag** — no brackets
7. **Reference issues** in the footer when applicable

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

## Auto-Push

After every commit, automatically push to the current branch:

```bash
git push origin <current-branch>
```

**Do not** ask for confirmation. Always push after commit.

## Branch Strategy

- **Never create new branches** for changes
- Always work on the current active branch
- At the end, report which branch the commit was pushed to

## Release Workflow (develop → main)

### `#release` Command

When the user types `#release`, execute the **full release cycle** automatically:

1. **Determine version** — Automatically analyze commits since last tag and determine version (major/minor/patch) based on semantic versioning rules. Only ask user if they explicitly specify a version (e.g., `#release v1.2.3`)
2. **Check branch** — Ensure current branch is `develop` and working tree is clean
3. **Merge develop → main**
4. **Generate changelog** from commits since last tag
5. **Update package.json** with new version
6. **Create release commit** with changelog
7. **Tag** with `v<version>`
8. **Push** main + tag
9. **Switch back** to develop

### Automated Version Determination

**DO NOT ask the user for version type.** Automatically determine the version by analyzing commits since the last tag:

```bash
# Get commits since last tag
COMMITS=$(git log $(git describe --tags --abbrev=0 2>/dev/null || echo "v0.0.0")..HEAD --pretty=format:"%s" --no-merges)

# Analyze commit types and determine version
if echo "$COMMITS" | grep -qE "^[💥🔥⚡]"; then
  # Breaking changes → Major
  VERSION_TYPE="major"
elif echo "$COMMITS" | grep -qE "^✨"; then
  # New features → Minor
  VERSION_TYPE="minor"
else
  # Bug fixes, docs, chore, etc. → Patch
  VERSION_TYPE="patch"
fi
```

**Version determination rules:**
| Commit Icon | Version Bump | Description |
|-------------|--------------|-------------|
| 💥 or 🔥 | Major | Breaking changes |
| ✨ | Minor | New features |
| 🐛, 📚, 💅, 🔧, ⚡, ✅, 📦, 🔨, 🧹, ⏪ | Patch | Bug fixes, docs, maintenance, etc. |

### Explicit Version Override

If the user specifies a version explicitly (e.g., `#release v2.0.0`), use that version instead of auto-determining:

```bash
# Parse user-specified version
if [[ "$INPUT" =~ v([0-9]+)\.([0-9]+)\.([0-9]+) ]]; then
  MAJOR=${BASH_REMATCH[1]}
  MINOR=${BASH_REMATCH[2]}
  PATCH=${BASH_REMATCH[3]}
  VERSION="$MAJOR.$MINOR.$PATCH"
else
  # Auto-determine version from commits
  # ... (see Automated Version Determination above)
fi
```

### Automated Release Steps

```bash
# 1. Get current version from latest tag
CURRENT=$(git describe --tags --abbrev=0 2>/dev/null || echo "v0.0.0")
CURRENT_VERSION=${CURRENT#v}

# 2. Analyze commits and determine new version
COMMITS=$(git log $CURRENT..HEAD --pretty=format:"%s" --no-merges)
# ... (determine VERSION_TYPE from commits)

# 3. Calculate new version based on VERSION_TYPE
IFS='.' read -r MAJOR MINOR PATCH <<< "$CURRENT_VERSION"
case $VERSION_TYPE in
  major) MAJOR=$((MAJOR + 1)); MINOR=0; PATCH=0 ;;
  minor) MINOR=$((MINOR + 1)); PATCH=0 ;;
  patch) PATCH=$((PATCH + 1)) ;;
esac
NEW_VERSION="$MAJOR.$MINOR.$PATCH"

# 4. Ensure clean working tree on develop
git checkout develop
git pull origin develop

# 5. Merge into main
git checkout main
git merge develop --no-ff -m "Merge develop into main for release"

# 6. Update package.json with new version (if file exists)
if [ -f "package.json" ]; then
  sed -i "s/\"version\": \"$CURRENT_VERSION\"/\"version\": \"$NEW_VERSION\"/" package.json
  git add package.json
fi

# 7. Generate changelog from commits since last tag
LOG=$(git log $CURRENT..HEAD --pretty=format:"%s" --no-merges)
# Parse into categories (✨ feat, 🐛 fix, 🔧 refactor, 📚 docs, 🧹 chore)
# Format each commit as: • (scope): [ai-flag] description
# Group by type under header: {icon} {type name}

# 8. Create release commit
git commit --allow-empty -m "🚀 Release v$NEW_VERSION

✨ New features
• (auth): [opencode] add OAuth login
• (ui): [opencode] add dark mode toggle

🐛 Bug fixes
• (api): [opencode] solve timeout issue

🔧 Refactor
• (store): [opencode] simplify state management
"

# 9. Tag
git tag -a v$NEW_VERSION -m "Release v$NEW_VERSION"

# 10. Push
git push origin main
git push origin v$NEW_VERSION

# 11. Switch back to develop
git checkout develop
```

### Changelog Format

The release commit message MUST follow this structure. Each commit type gets its own section with a header showing the icon and type name. Under each header, list **one bullet per commit** using Discord's native list format (`•`).

**Format per line:**
```
• ({scope alterado}): [AI flag se existir] {descrição da alteração}
```

**Full structure:**
```
🚀 Release v<version>

✨ New features
• (scope): [ai-flag] description of feature
• (scope): [ai-flag] description of another feature

🐛 Bug fixes
• (scope): [ai-flag] description of fix

🔧 Refactor
• (scope): [ai-flag] description of refactor

📚 Documentation
• (scope): [ai-flag] description of docs change

🧹 Maintenance
• (scope): [ai-flag] description of chore
```

**Rules:**
1. **Header** = `{icon} {type name}` (e.g., `✨ New features`)
2. **One bullet per commit** — each commit gets its own `•` line
3. **Scope always in parentheses** — `• (auth): ...` not `• auth: ...`
4. **AI flag only if AI made the commit** — omit for human commits
5. **Group by type** — if there are 3 features and 2 fixes, you get 2 sections (Features and Bug fixes) with 3 and 2 bullets respectively
6. **Skip empty sections** — if no docs changes, don't include the Documentation header
7. **Description** — keep the original commit message (imperative mood, English)

**Example with mixed commits:**
```
🚀 Release v1.2.0

✨ New features
• (auth): [opencode] add OAuth login with Google
• (ui): [opencode] add dark mode toggle
• (api): [opencode] add rate limiting endpoint

🐛 Bug fixes
• (api): [opencode] solve timeout on user endpoint
• (auth): [opencode] fix token refresh race condition
• (ui): fix button alignment on mobile

🔧 Refactor
• (store): [opencode] simplify state management
```

### Versioning Rules

| Version   | When to use                         | Example           |
| --------- | ----------------------------------- | ----------------- |
| **Major** | Breaking API changes                | `1.0.0` → `2.0.0` |
| **Minor** | New features (backwards compatible) | `1.0.0` → `1.1.0` |
| **Patch** | Bug fixes (backwards compatible)    | `1.0.0` → `1.0.1` |

### Release Checklist

- [ ] All features merged from develop
- [ ] Version automatically determined from commits
- [ ] package.json updated with new version
- [ ] Release commit created with proper format and changelog
- [ ] Pushed to main
- [ ] Tag created: `git tag v<version>`
- [ ] Switched back to develop

## Workflow

1. Run `git status` before making changes
2. Preserve existing changes not made by you
3. Edit only the necessary files
4. Run build/lint/test checks
5. Stage only files related to the change
6. Create commit with proper message format (including AI flag if applicable)
7. Push to current branch
8. Report completion status

## Examples

### Feature Addition (AI commit)

```bash
git add src/components/Header.tsx
git commit -m "✨ (ui): [codebuff] add user avatar display"
git push origin main
```

### Bug Fix (AI commit)

```bash
git add src/services/api.ts
git commit -m "🐛 (api): [codebuff] solve timeout on user endpoint"
git push origin main
```

### Refactor (AI commit)

```bash
git add src/hooks/useAuth.ts src/utils/auth.ts
git commit -m "🔧 (auth): [codebuff] simplify authentication flow"
git push origin main
```

### Skill Addition (AI commit)

```bash
git add .agents/skills/vercel-deploy/SKILL.md
git commit -m "✨ (skill): [codebuff] add vercel-deploy skill"
git push origin main
```

### Human Commit (no flag)

```bash
git add src/styles.css
git commit -m "💅 (ui): update button border radius"
git push origin develop
```
