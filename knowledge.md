# Knowledge

## Skills

- Always follow the instructions of installed skills in `.agents/skills/`
- When a skill applies to the current task, load and follow its workflow completely
- Never skip skill steps — execute them in order as documented

## Commit Pattern

When committing code, follow the rules defined in `skills/commit-pattern/SKILL.md` of this repository. Do NOT install the skill — just read and apply the rules directly.

Key rules:
- Use icon-based format: `<icon> (<scope>): [<ai-flag>] <message in English>`
- Always include the AI flag (e.g., `[opencode]`) for AI commits
- Run pre-commit checks (lint, typecheck, tests) before committing
- Auto-push after every commit
- For `#release`, generate the changelog using Discord bullet format (`•`): `• (scope): [ai-flag] description` — one bullet per commit, grouped by type
