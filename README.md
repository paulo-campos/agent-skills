# agent-skills

> Reusable AI agent skills for coding workflows. Install via `npx skills add`.

## 📦 Available Skills

| Skill | Description | Documentation |
|-------|-------------|---------------|
| `commit-pattern` | Standardized commit workflow with icon-based conventional commits, AI identifier flags, and release automation | [Detailed docs](docs/commit-pattern.md) |
| `project-docs` | Generate and maintain comprehensive project documentation | [Detailed docs](skills/project-docs/SKILL.md) |
| `vercel-deploy` | Deploy to Vercel with automatic Discord notifications for deployment status | [Detailed docs](docs/vercel-deploy.md) |

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

[Read full documentation →](docs/commit-pattern.md)

### project-docs

Generate and maintain comprehensive project documentation:
- Automatic documentation creation when missing
- Updates when project changes
- Icon-based section formatting
- Structured documentation templates

[Read full documentation →](skills/project-docs/SKILL.md)

### vercel-deploy

Deploy to Vercel with Discord notifications:
- Automatic notifications on every deploy
- Staging/Production channel routing
- First-time setup wizard
- GitHub Actions workflow generation

[Read full documentation →](docs/vercel-deploy.md)

## 📁 Structure

```
agent-skills/
├── docs/
│   ├── commit-pattern.md
│   └── vercel-deploy.md
├── skills/
│   ├── commit-pattern/
│   │   └── SKILL.md
│   ├── project-docs/
│   │   └── SKILL.md
│   └── vercel-deploy/
│       └── SKILL.md
├── .agents/
│   └── skills/
│       ├── commit-pattern/
│       └── project-docs/
├── AGENTS.md
├── knowledge.md
├── README.md
├── skills-lock.json
├── metadata.json
└── LICENSE
```

## 📄 License

MIT
