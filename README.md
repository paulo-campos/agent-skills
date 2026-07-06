# agent-skills

> Reusable AI agent skills for coding workflows. Install via `npx skills add`.

## Available Skills

| Skill | Description |
|-------|-------------|
| `commit-pattern` | Standardized commit workflow with icon-based conventional commits, AI identifier flags, and release automation |
| `vercel-deploy` | Deploy to Vercel with automatic Discord notifications for deployment status |

## Installation

```bash
# Install a specific skill
npx skills add paulo-campos/agent-skills --skill commit-pattern --yes
npx skills add paulo-campos/agent-skills --skill vercel-deploy --yes
```

## Skills Overview

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
- Release workflow via `#release` command
- Pre-commit checks (lint, typecheck, tests)

### vercel-deploy

Deploy to Vercel with Discord notifications:
- Automatic notifications on every deploy
- Staging/Production channel routing
- First-time setup wizard
- GitHub Actions workflow generation

## Structure

```
agent-skills/
├── skills/
│   ├── commit-pattern/
│   │   └── SKILL.md
│   └── vercel-deploy/
│       └── SKILL.md
├── README.md
├── metadata.json
└── LICENSE
```

## License

MIT
