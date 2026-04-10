---
name: migrateDocumentation
description: Split a single README.md into stakeholder-focused README.md and technical DEVELOPER.md — classifies content, asks clarifying questions, and generates both files. Use when a project has a monolithic README that needs restructuring.
---
<!-- Version: 2026-03-08.1 -->

# Migrate Documentation — Split README into Two Files

Migrate from a single README.md to a two-file documentation structure: stakeholder-focused README.md and technical DEVELOPER.md.

## Process

### Step 1: Project Analysis

1. **Read current README.md** — extract all content
2. **Scan project structure** — look for context in docs, components, infrastructure
3. **Check for existing DEVELOPER.md** — avoid overwriting

### Step 2: Content Classification

**Technical Content (moves to DEVELOPER.md):**
- Development setup instructions
- CDK/deployment commands
- Deployment procedures
- Architecture diagrams and technical details
- Code structure explanations
- Prerequisites and dependencies
- Testing instructions
- Troubleshooting guides

**Business Content (stays in README.md):**
- Project description and purpose
- Business value statements
- How it works (in business terms)
- Integration explanations
- Contact information

### Step 3: Clarifying Questions

Ask to fill gaps for stakeholder documentation:

1. **Business Purpose**: "In simple terms, what business problem does this solve?"
2. **Key Benefits**: "What are the top 3 benefits? Can you quantify any?"
3. **Target Users**: "Who benefits from this system?"
4. **Input/Output**: "What triggers this system and what does it produce?"
5. **Integration Context**: "How does this connect to other systems?"

### Step 4: Generate README.md (Stakeholder-focused)

```markdown
# [Project Name]

## What It Does
[Clear business description]

## Business Value
**Problem Solved**: [Based on user input]

**Key Benefits**:
- [Benefit 1 — quantified if possible]
- [Benefit 2 — quantified if possible]
- [Benefit 3 — quantified if possible]

## How It Works
1. **Input**: [What triggers the system]
2. **Processing**: [What happens — business terms]
3. **Output**: [What result is produced]

## Integration
- [System 1]: [Connection and purpose]
- [System 2]: [Connection and purpose]

## Status & Metrics
**Current Status**: [Active/Development/Testing]
**Last Updated**: [current-date]

## Questions?
For technical implementation details, see [DEVELOPER.md](DEVELOPER.md).
```

### Step 5: Generate DEVELOPER.md (Technical)

```markdown
# [Project Name] — Developer Guide

## Architecture Overview
[Based on analysis of project structure and infrastructure]

## Development Setup
[Migrated from README]

## Deployment
[Migrated from README]

## Project Structure
[Generated from actual folder structure]

## Commands Reference
[Migrated from README]

## Testing
[Migrated from README]

## Monitoring
[Based on infrastructure analysis]

## Troubleshooting
[Migrated from README]

---
**Last Updated**: [current-date]
```

### Step 6: Backup and Replace

1. **Backup original** as `README.md.backup`
2. **Create new README.md** with stakeholder content
3. **Create DEVELOPER.md** with technical content
4. **Confirm with user** before finalizing

## Writing Standards

- Use active voice — make clear who performs the action
- Replace subjective descriptors with concrete examples
- Quantify claims with specific data
- Eliminate weasel words and hedge phrases
- Be direct and precise
