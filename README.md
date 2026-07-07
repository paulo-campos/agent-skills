# agent-skills

> Reusable AI agent skills for coding workflows. Install via `npx skills add`.

## 📦 Available Skills

| Skill | Description | Documentation |
|-------|-------------|---------------|
| `commit-pattern` | Use when committing code to apply standardized commit format with icons, scope, AI flags, run quality checks before commit, and auto-push. | [Detailed docs](skills/commit-pattern/README.md) |
| `project-docs` | Automatically maintains project documentation up-to-date whenever code changes affect documented features. Creates and updates vision, architecture, roadmap, changelog, and decisions. | [Detailed docs](skills/project-docs/README.md) |
| `vercel-deploy` | Automatically deploys to Vercel with Discord notifications via GitHub Actions. Includes setup wizard, staging/production routing, and troubleshooting. | [Detailed docs](skills/vercel-deploy/README.md) |

## 🚀 Installation

```bash
# Install a specific skill
npx skills add paulo-campos/agent-skills --skill commit-pattern --yes
npx skills add paulo-campos/agent-skills --skill project-docs --yes
npx skills add paulo-campos/agent-skills --skill vercel-deploy --yes
```

## 🔍 Skills Overview

### commit-pattern

Standardized commit format:
```
<icon> (<scope>): [<ai-flag>] <message>
```

Example:
```bash
✨ (auth): [codebuff] add OAuth login
🐛 (api): [codebuff] solve timeout on endpoint
```

Features:
- Icon-based conventional commits (✨ feat, 🐛 fix, 🔧 refactor, etc.)
- AI identifier flags ([codebuff], [claude], [cursor], [copilot])
- Default branch strategy (assumes `develop` when not specified)
- Release workflow via `#release` command
- Pre-commit checks (lint, typecheck, tests)

[Read full documentation →](skills/commit-pattern/README.md)

### project-docs

Automatically maintains project documentation up-to-date:
- Documentation is created when missing
- Documentation is updated when project changes
- Automatic updates whenever code changes affect documented features
- Icon-based section formatting
- Structured documentation templates

[Read full documentation →](skills/project-docs/README.md)

### vercel-deploy

Deploy to Vercel with Discord notifications:
- Automatic notifications on every deploy
- Staging/Production channel routing
- First-time setup wizard
- GitHub Actions workflow generation

[Read full documentation →](skills/vercel-deploy/README.md)

## 📁 Structure

```
agent-skills/
├── skills/
│   ├── commit-pattern/
│   │   ├── SKILL.md
│   │   └── README.md
│   ├── project-docs/
│   │   ├── SKILL.md
│   │   └── README.md
│   └── vercel-deploy/
│       ├── SKILL.md
│       └── README.md
├── .agents/
│   └── skills/
│       ├── commit-pattern/
│       └── project-docs/
├── AGENTS.md
├── knowledge.md
├── README.md
├── metadata.json
└── LICENSE
```

## 📄 License

MIT
