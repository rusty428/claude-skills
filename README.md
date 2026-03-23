# claude-skills

A curated collection of Claude Code skills for software development workflows — task management, documentation generation, weekly reporting, code scaffolding, and writing standards.

## What Are Claude Code Skills?

Skills are markdown files with YAML frontmatter that Claude Code loads on demand. Place them in your `.claude/commands/` directory, and they become available as slash commands (e.g., `/createTodo`). Skills standardize repeatable workflows so you get consistent, high-quality output every time.

## Skills Included

### Task Management
| Skill | Purpose |
|-------|---------|
| **createTodo** | Create a structured task planning document before implementation |
| **createQuickFix** | Document a minor bug fix with root cause analysis and code diffs |
| **createImplementationPlan** | Write a detailed implementation plan with steps, testing strategy, and success criteria |
| **createSprintLog** | Log completed work with summary, stakeholder highlights, and technical details |

### Documentation
| Skill | Purpose |
|-------|---------|
| **updateProjectDocs** | Generate or update project-level README.md and DEVELOPER.md from code analysis |
| **updateCdkDocs** | Generate CDK infrastructure documentation from stack analysis |
| **updateLambdaDocs** | Generate Lambda function documentation from handler and CDK analysis |
| **migrateDocumentation** | Split a monolithic README into stakeholder README.md + technical DEVELOPER.md |

### Weekly Reporting
| Skill | Purpose |
|-------|---------|
| **weeklyAnalysis** | Scan and classify the week's development activity by impact level (HIGH/MEDIUM/LOW) |
| **weeklyReport** | Create a stakeholder-ready weekly report from an approved analysis |

### Writing & Quality
| Skill | Purpose |
|-------|---------|
| **noWeaselWords** | Apply precise writing standards — eliminate vague language, quantify claims |
| **cleanitup** | Review and clean up any document to meet writing standards |

### Workflow
| Skill | Purpose |
|-------|---------|
| **gitflow** | Automate GitHub issue and PR workflow — create issues, suggest branches, ensure PRs reference issues |
| **troubleshooting** | Structured debugging protocol: observe, compare, collaborate, hypothesize, verify, implement |
| **reviewProject** | Quick project context establishment at the start of a session |
| **createLambda** | Scaffold a new Lambda function with handler + functions/ separation of concerns |

## Installation

### Download individual skills (recommended)

Download the skills you want directly into your project's `.claude/commands/` directory:

```bash
mkdir -p .claude/commands
curl -o .claude/commands/createTodo.md https://raw.githubusercontent.com/rusty428/claude-skills/main/createTodo.md
```

Each `.md` file is self-contained — drop it in and it's available as a slash command.

### Clone the full repo

```bash
git clone https://github.com/rusty428/claude-skills.git
```

Then copy whichever skills you need into your project's `.claude/commands/` directory.

## Skill Format

Each skill is a standalone markdown file with YAML frontmatter:

```yaml
---
name: skillName
description: One-line description used for trigger matching
---
<!-- Version: YYYY-MM-DD.N -->

# Skill Title

Instructions that Claude follows when the skill is invoked.
```

- **name**: How Claude identifies and invokes the skill
- **description**: Used for matching when Claude decides which skill applies
- **Version comment**: Tracks when the skill was last updated (format: `YYYY-MM-DD.N` where N is the revision number for that date)

## Writing Standards

These skills enforce precise technical writing throughout all generated documentation:

- **No weasel words** — vague qualifiers like "basically," "generally," "should" are eliminated
- **Quantified claims** — words like "faster," "better," "significant" require specific measurements
- **Active voice** — clear subject-verb-object structure showing who did what
- **No hedge phrases** — "I think," "I believe," "in my opinion" are removed
- **Substantiated benefits** — only claim improvements backed by evidence

## License

MIT License. See [LICENSE](LICENSE) for details.
