---
name: createSprintLog
description: Create a development log after completing a task — documents accomplishments, technical details, stakeholder highlights, and next steps. Use after finishing a sprint or significant piece of work.
---
<!-- Version: 2026-03-08.1 -->

# Create Sprint Log

Create a development log documenting what was accomplished in the task just completed.

## Process

1. Check that a `docs/daily/` folder exists (create if needed)
2. Run `date` to get current date — format as YYYY-MM-DD
3. Create a markdown file documenting what was accomplished

Write in a direct, technical style — like actual developer notes. Be precise, eliminate weasel words, and quantify claims with specific data. Include one section written in a more engaging style for stakeholder communication.

## Document Structure

```markdown
# Development Update: %short_title%

**Date:** YYYY-MM-DD
**Task:** [task description]
**Project:** [project folder name — ask if uncertain]
**Project Code:** [if from implementation or todo, use that code; otherwise generate: "project_name"-YYYYMMDD-HHMMSS]
**Category:** [Minor Update | Bug Fix | New Feature | Documentation | Tech Debt | Configuration | Security | Performance]
**Status:** COMPLETED

## Summary
[Brief overview suitable for stand-up meetings. Structure each achievement as a complete narrative covering: WHAT was done (the specific change), WHY it was done (the problem it solves), and WHO it helps (the concrete benefit). Write coherent sentences — not bullet-point style. Apply precise writing standards strictly.]

## Stakeholder Highlight
[Engaging style suitable for retrospectives, demos, or stakeholder updates. Focus on technical progress and code improvements. Present as flowing narrative incorporating WHAT was accomplished, WHY it was necessary, and WHO benefits. Professional tone, accessible to non-technical stakeholders. Even here — eliminate weasel words and be precise.]

### Benefits
*Practical improvements or fixes achieved.*

### Challenges
*Issues encountered and how they were handled.*

## Technical Details
*Keep these sections direct and factual*

### Changes Made
*Schema modifications, infrastructure changes, code changes, etc.*

### Workflow Changes
*Process improvements, automation added, etc.*

## Next Steps
*Immediate follow-up tasks or known issues.*
```

## Cross-Reference Updates

- If this sprint was based on an **IMPLEMENTATION file**: update its status to "COMPLETED", add this filename to its Reference section, then move to `docs/working/completed/`
- If this sprint started with a **TODO file**: update its status to "COMPLETED", add this filename to its Reference section, then move to `docs/todo/completed/`

## Documentation Updates

Review project documentation and update as needed:

- **README.md** (Stakeholder-focused): Update only if changes affect business value, user experience, or overall system purpose
- **DEVELOPER.md** (Technical): Update if changes affect architecture, deployment procedures, development setup, or technical operations

**Update Guidelines:**
- Minor code changes or bug fixes: No documentation updates needed
- New features or capabilities: Update README.md business benefits section
- Architecture changes: Update DEVELOPER.md architecture and setup sections
- New deployment procedures: Update DEVELOPER.md deployment section

## Rules

- **File naming:** `YYYY-MM-DD-UPDATE-summary-title.md`
- Apply precise writing standards throughout — no weasel words
- Quantify all claims with specific data
- Use active voice showing who performed the action
