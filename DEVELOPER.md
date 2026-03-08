# claude-skills — Developer Guide

## Adding a New Skill

### 1. Create the file

Create a new `.md` file in the repo root. Name it after the skill (camelCase):

```bash
touch myNewSkill.md
```

### 2. Add frontmatter

Every skill requires YAML frontmatter with `name` and `description`:

```yaml
---
name: myNewSkill
description: One-line description of what this skill does and when to use it
---
<!-- Version: YYYY-MM-DD.1 -->
```

**Description guidelines:**
- Start with an action verb ("Create", "Generate", "Apply", "Scaffold")
- Include when to trigger ("Use when...", "Use after...")
- Keep it under 200 characters — this is what Claude uses to decide if a skill applies

### 3. Write the instructions

Structure the skill body as instructions Claude follows:

```markdown
# Skill Title

Brief intro — what this skill produces.

## Process

1. Step one
2. Step two
3. Step three

## Document Structure (if the skill generates a file)

\`\`\`markdown
# Template
[structure here]
\`\`\`

## Rules

- Constraints and guidelines
- What NOT to do
```

### 4. Version it

Use the version comment format: `<!-- Version: YYYY-MM-DD.N -->`

- Increment N for same-day revisions
- Reset to 1 for new dates

## Conventions

### Naming
- **File name**: camelCase matching the skill name (e.g., `createTodo.md`)
- **Skill name**: Same as file name, used in frontmatter `name:` field

### Structure
- Keep skills self-contained — don't reference other skills inline
- Embed writing standards directly in skills that produce documentation
- Use `docs/` subdirectories for generated files (not `projectDocs/`):
  - `docs/daily/` — sprint logs, quick fixes
  - `docs/weekly/` — analysis, reports
  - `docs/todo/` — task planning documents
  - `docs/working/` — implementation plans

### Writing Standards
Skills that produce documentation should enforce these inline:
- No weasel words (see `noWeaselWords.md` for the full list)
- Quantified claims with specific numbers
- Active voice throughout
- No hedge phrases or disclaimers

### File Naming in Generated Docs
Skills that create files follow this convention:
- `YYYY-MM-DD-UPDATE-summary-title.md` — sprint logs
- `YYYY-MM-DD-QUICKFIX-brief-description.md` — quick fixes
- `YYYY-MM-DD-TODO-task-title.md` — todos
- `YYYY-MM-DD-IMPLEMENTATION-task-title.md` — implementation plans
- `YYYY-MM-DD-WEEKLY-ANALYSIS.md` — weekly analysis
- `YYYY-MM-DD-WEEKLY-REPORT.md` — weekly reports

## Modifying Existing Skills

1. Read the current skill file
2. Make targeted changes
3. Update the version comment
4. Test by invoking the skill in a Claude Code session
5. Commit with a descriptive message

## Project Structure

```
claude-skills/
├── README.md              # What this repo is, skill catalog, installation
├── DEVELOPER.md           # This file — how to add/modify skills
├── LICENSE                # MIT License
├── cleanitup.md
├── createImplementationPlan.md
├── createLambda.md
├── createQuickFix.md
├── createSprintLog.md
├── createTodo.md
├── migrateDocumentation.md
├── noWeaselWords.md
├── reviewProject.md
├── troubleshooting.md
├── updateCdkDocs.md
├── updateLambdaDocs.md
├── updateProjectDocs.md
├── weeklyAnalysis.md
└── weeklyReport.md
```

## Testing Skills

1. Open a Claude Code session in a project
2. Ensure the skills repo is in your workspace tree (or skills are copied locally)
3. Invoke the skill: `/skillName` or let Claude auto-trigger it
4. Verify the output matches expectations
5. Check generated files follow naming conventions and writing standards

---
**Last Updated:** 2026-03-08
