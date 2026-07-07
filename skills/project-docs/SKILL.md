---
name: project-docs
description: Automatically maintains project documentation up-to-date whenever code changes affect documented features. Creates and updates vision, architecture, roadmap, changelog, and decisions.
---

# Project Docs

Generate, maintain, and synchronize project documentation automatically.

## Goal

Generate and maintain complete and up-to-date project documentation, including vision, architecture, roadmap, changelog, and decisions.

## Intake

Project name (optional - if not provided, detects automatically).

## Output Contract

Complete documentation structure including README.md, PROJECT_VISION.md, ARCHITECTURE.md, ROADMAP.md, CHANGELOG.md, DECISIONS.md, STATUS.md, and GUIDES/ (getting-started, development, deployment). All with icons and detailed content.

## Quality Bar

- All files must have icons
- Links working
- No duplicate content
- README.md linking to all docs
- Tables properly formatted
- Status up-to-date
- Changelog up-to-date
- Decisions with ADRs
- If any check fails, report the issue and do not deliver until fixed

## 🎯 Purpose

Every project must have clear, complete, and up-to-date documentation:

1. Documentation is created when missing
2. Documentation is updated when project changes
3. All sections use icons for visual clarity
4. Every detail is documented thoroughly

## 📋 When to Use

- **No documentation exists** → Create full documentation
- **Project changes** → Update affected documentation
- **New feature added** → Document it
- **Bug fixed** → Update changelog
- **Decision made** → Create ADR if architectural

## 📁 Documentation Structure

Every project MUST have this structure:

```
project/
├── README.md                    # Entry point
├── docs/
│   ├── PROJECT_VISION.md        # What and why
│   ├── ARCHITECTURE.md          # How it works
│   ├── ROADMAP.md               # What's planned
│   ├── CHANGELOG.md             # What changed
│   ├── DECISIONS.md             # Why we chose this
│   ├── STATUS.md                # Current progress
│   └── GUIDES/                  # How-to guides
│       ├── getting-started.md
│       ├── development.md
│       └── deployment.md
```

## 🎨 Icon System

**ALWAYS** use icons in section titles. Required icons:

| Section | Icon | Description |
|---------|------|-------------|
| Vision | 👁️ | Project overview and goals |
| Architecture | 🏗️ | Technical structure |
| Roadmap | 🗺️ | Planned phases |
| Status | 📊 | Current progress |
| Changelog | 📝 | Change history |
| Decisions | 🔍 | Architecture decisions |
| Guides | 📖 | How-to documentation |
| Features | ✨ | New capabilities |
| Bug Fixes | 🐛 | Problem solutions |
| Refactor | 🔧 | Code improvements |
| Performance | ⚡ | Speed optimizations |
| Tests | ✅ | Testing |
| Build | 📦 | Build system |
| CI/CD | 🔨 | Continuous integration |
| Chore | 🧹 | Maintenance |
| Security | 🔒 | Security measures |
| Database | 💾 | Data storage |
| API | 🔌 | External connections |
| Auth | 🔑 | Authentication |
| UI | 🎨 | User interface |
| Config | ⚙️ | Configuration |
| Dependencies | 📚 | Libraries and packages |
| Deployment | 🚀 | Going to production |

## 📝 Documentation Rules

### 1. README.md (Entry Point)

```markdown
# Project Name

## 🎯 What is this
Brief description (1-2 sentences)

## 🚀 Quick Start
How to run in 3 steps

## 📚 Documentation
- [Vision](docs/PROJECT_VISION.md) - Why this exists
- [Architecture](docs/ARCHITECTURE.md) - How it works
- [Roadmap](docs/ROADMAP.md) - What's planned

## 🤝 Contributing
How to contribute

## 📄 License
License type
```

### 2. PROJECT_VISION.md

```markdown
# 🎯 Project Vision

## 📋 Overview
What this project is

## 🎯 Goals
What we want to achieve

## 👥 Target Users
Who will use this

## 💡 Problems Solved
What problems we solve

## 🔮 Future Vision
Where this is going
```

### 3. ARCHITECTURE.md

```markdown
# 🏗️ Architecture

## 📊 Overview
High-level architecture diagram

## 🧩 Components
What each part does

## 🔄 Data Flow
How data moves

## 🔌 Integrations
External services

## 💾 Storage
Data persistence

## 🔒 Security
Security measures

## ⚡ Performance
Performance considerations
```

### 4. ROADMAP.md

```markdown
# 🗺️ Roadmap

## 📊 Progress
Overall progress bar

## 📋 Phases

### Phase 1: Name
- [x] Task 1
- [ ] Task 2

### Phase 2: Name
- [ ] Task 1
- [ ] Task 2
```

### 5. STATUS.md

```markdown
# 📊 Status

## 🎯 Current Phase
What we're working on now

## 📈 Progress
Progress by area

## 🔄 Last Updated
When this was last updated

## 📋 Next Steps
What's coming next
```

### 6. CHANGELOG.md

```markdown
# 📝 Changelog

## [Unreleased]

### ✨ Features
- Feature description

### 🐛 Bug Fixes
- Fix description

### 🔧 Refactor
- Refactor description

### 📚 Documentation
- Doc description

### 🧹 Chore
- Chore description
```

### 7. DECISIONS.md

```markdown
# 🔍 Architecture Decisions

## ADR-001: Decision Title

### 📋 Status
Accepted

### 📊 Context
Why this decision was needed

### 💡 Decision
What we decided

### 🎯 Consequences
What this means for the project
```

## 🔄 Workflow

### When No Documentation Exists

1. **Analyze project structure**
   - Scan for code files
   - Identify main components
   - Understand architecture

2. **Create documentation structure**
   - Create `docs/` folder
   - Create all required files
   - Use icons in all sections

3. **Document everything**
   - Vision: What and why
   - Architecture: How it works
   - Roadmap: What's planned
   - Status: Current state

4. **Create entry point**
   - Write README.md
   - Link to all documentation

### When Project Changes

1. **Identify what changed**
   - New feature → Update ROADMAP.md
   - Bug fix → Update CHANGELOG.md
   - Decision → Create ADR

2. **Update affected documentation**
   - README.md if structure changed
   - ARCHITECTURE.md if components changed
   - STATUS.md if progress changed

3. **Maintain consistency**
   - Use same icons
   - Follow same format
   - Keep same structure

## ✅ Quality Checklist

Before finalizing documentation:

- [ ] All sections have icons
- [ ] README.md links to all docs
- [ ] No duplicate content
- [ ] All code blocks have language
- [ ] Tables are properly formatted
- [ ] Links are working
- [ ] Status is current
- [ ] Changelog is updated
- [ ] Decisions have ADRs
- [ ] Everything is detailed

## 🔗 Integration with Other Skills

### With commit-pattern

When updating documentation, use commit-pattern format:

```bash
📚 (docs): [ai-flag] update architecture documentation
✨ (docs): [ai-flag] add new feature to roadmap
🐛 (docs): [ai-flag] fix outdated changelog
```

### With code changes

Always update documentation when code changes:

1. Code change → Check if documentation needs update
2. Update documentation → Use commit-pattern
3. Keep everything synchronized
