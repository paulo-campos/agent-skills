# agent-skills

> Reusable AI agent skills for coding workflows. Install via `npx skills add`.

## 📦 Available Skills

| Skill | Description | Documentation |
|-------|-------------|---------------|
| `commit-pattern` | Standardized commit workflow with icon-based conventional commits, AI identifier flags, and release automation | [Detailed docs](skills/commit-pattern/README.md) |
| `project-docs` | Generate and maintain comprehensive project documentation | [Detailed docs](skills/project-docs/README.md) |
| `vercel-deploy` | Deploy to Vercel with automatic Discord notifications for deployment status | [Detailed docs](skills/vercel-deploy/README.md) |

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

Generate and maintain comprehensive project documentation:
- Automatic documentation creation when missing
- Updates when project changes
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
├── skills-lock.json
├── metadata.json
└── LICENSE
```

## 📄 License

MIT
