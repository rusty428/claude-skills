---
name: reviewProject
description: Quick project context establishment — scan project structure, identify type and key components, confirm readiness. Use at the start of a session to orient on an unfamiliar project.
---
<!-- Version: 2026-03-08.1 -->

# Review Project — Quick Context Establishment

Quickly establish project context without overwhelming detail.

## Process

### Step 1: Project Identification
1. **Extract project name** from current directory
2. **Scan root directory** — list key files and folders
3. **Identify project type** based on file patterns and structure

### Step 2: Structure Review

Look for these indicators:
- **CDK/Infra projects**: `cdk.json`, `bin/`, `lib/`, `tsconfig.json`, `pnpm-workspace.yaml`
- **Web applications**: `package.json`, `src/`, `public/`, framework config files
- **Lambda projects**: `lambdas/` or `functions/` directory with handlers
- **Documentation projects**: `docs/`, `content/`, markdown-heavy structure
- **Monorepos**: `packages/`, workspace config, multiple `package.json` files

### Step 3: Context Acknowledgment

Provide brief confirmation:

```
Project Context Established

Project: [ProjectName]
Type: [CDK Module / Web Application / Lambda Service / Monorepo / etc.]
Key Components: [2-3 main elements identified]
Docs: [CLAUDE.md / docs/memory.md / README.md — note which exist]

Ready to work on this project.
```

## Rules

- **Be concise** — no detailed summaries, just essential awareness
- **Read `docs/memory.md`** if it exists — this contains portable project knowledge
- **Read `CLAUDE.md`** if it exists — this contains project-specific instructions
- **Focus on structure** — identify what type of project and main components
- **Don't scan deeply** — this is orientation, not exploration
