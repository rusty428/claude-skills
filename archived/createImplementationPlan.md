---
name: createImplementationPlan
description: Create a detailed implementation plan before starting work — captures problem statement, step-by-step approach, testing strategy, and success criteria. Use before touching code on any non-trivial task.
---
<!-- Version: 2026-03-08.1 -->

# Create Implementation Plan

Create a working implementation plan before writing any code.

## Process

1. Check that a `docs/working/` folder exists (create if needed)
2. Run `date` to get current date — format as YYYY-MM-DD
3. Create one implementation plan using today's date and task title

Write a comprehensive technical plan that serves as the working reference. Make it detailed enough that if the session restarts, all context is preserved.

## Document Structure

```markdown
# Implementation Plan: %short_title%

**Date:** YYYY-MM-DD
**Task:** %task_description%
**Project:** [project folder name — ask if uncertain]
**Project Code:** [if from TODO, use that code; otherwise generate: "project_name"-YYYYMMDD-HHMMSS]
**Category:** [if from TODO, use that value; otherwise: Minor Update | Bug Fix | New Feature | Documentation | Tech Debt | Configuration | Security | Performance]
**Status:** IN PROGRESS
**Reference:** [the sprint log / update file created from this document]

## Problem Statement
What exactly needs to be solved and why. Include current pain points and technical context.

## Implementation Plan
Step-by-step approach to solve the problem. Include:
- Specific files/components to modify
- Dependencies and prerequisites
- Order of operations
- Potential gotchas or considerations

### Order of Operations
1. [-] First step description
2. [-] Second step description
3. [-] Third step description

(Mark with [x] when completed, use 🔄 **CURRENT STEP** for active work)

### Testing Strategy
How to verify each step works correctly.

## Success Criteria
Concrete, measurable outcomes that define completion.

## Technical Notes
Relevant code snippets, API references, or architectural decisions to reference during implementation.
```

## Troubleshooting Protocol

When things don't go as planned:

1. **OBSERVE**: Gather all available information before suggesting changes
2. **COMPARE**: When a working example exists, directly compare ALL configuration aspects
3. **HYPOTHESIZE**: Present multiple possible causes and ask for input before proceeding
4. **VERIFY**: Confirm understanding before implementing changes
5. **IMPLEMENT**: Make minimal, focused changes that directly address the confirmed issue
6. **COLLABORATE**: Ask questions instead of making assumptions

**Do not modify any files until we have agreed on the exact cause and specific changes needed.**

## Rules

- **File naming:** `YYYY-MM-DD-IMPLEMENTATION-task-title.md`
- If built from a TODO file, update that file's status to "IN PROGRESS" and add this filename to its **Reference** field
- After the implementation plan is completed, **STOP. DO NOT START CHANGING CODE.**
- Wait for review and approval before proceeding
