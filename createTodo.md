---
name: createTodo
description: Create a structured todo/task document for planned work — captures problem statement, implementation approach, and technical notes. Use when planning a task before implementation.
---
<!-- Version: 2026-03-08.1 -->

# Create Todo

Create a structured task planning document for upcoming work.

## Process

1. Check that a `docs/todo/` folder exists (create if needed)
2. Run `date` to get current date — format as YYYY-MM-DD
3. Create one task document using today's date and task title

Write a comprehensive technical plan that serves as a working reference. Make it detailed enough to transform into an implementation document with all context preserved. Keep the tone direct and technical.

## Document Structure

```markdown
# Task: %short_title%

**Date:** YYYY-MM-DD
**Task:** %task_description%
**Project:** [project folder name — ask if uncertain]
**Project Code:** [generate: "project_name"-YYYYMMDD-HHMMSS]
**Requested By:** [ask who is requesting, default to current user]
**Category:** [Minor Update | Bug Fix | New Feature | Documentation | Tech Debt | Configuration | Security | Performance]
**Status:** SUBMITTED
**Reference:** [leave blank — other processes will link implementation/update files here]

## Problem Statement
What exactly needs to be solved and why. Include current pain points and technical context.

## High Level Implementation Plan
Approach to solve the problem. Include:
- Potential sharp edges or scenarios that may cause issues

## Technical Notes
Relevant code snippets, API references, or architectural decisions to reference during implementation.
```

## Rules

- **File naming:** `YYYY-MM-DD-TODO-task-title.md`
- Be thorough — this is the reference document for the entire task
- Don't create any other files unless specifically requested
- Apply precise writing standards — no weasel words, quantify claims
