---
name: createQuickFix
description: Create a quick fix log documenting a minor update or bug fix — captures root cause, solution, code changes, and impact. Use after completing a small fix that deserves documentation.
---
<!-- Version: 2026-03-08.1 -->

# Create Quick Fix Log

Document a minor update or bug fix with comprehensive technical detail.

## Process

1. Check that a `docs/daily/` folder exists (create if needed)
2. Run `date` to get current date — format as YYYY-MM-DD
3. Create a markdown file documenting the quick fix

Write in a direct, technical style with comprehensive detail. Include root cause analysis, technical implementation details, and quantified impact.

## Document Structure

```markdown
# Quick Fix: %short_description%

**Date:** YYYY-MM-DD
**Fix:** [brief description of what was fixed]
**Project:** [project folder name — ask if uncertain]
**Category:** [Minor Update | Bug Fix | New Feature | Documentation | Tech Debt | Configuration | Security | Performance]
**Status:** COMPLETED

## Problem
[Detailed explanation of the issue including WHY it occurred. Include technical details, root cause analysis, and specific examples like URLs, error messages, or component behavior.]

## Solution
[What was changed and why. Include quantified benefits with specific examples ONLY if substantiated. No unsubstantiated claims.]

## Files Changed
- `path/to/file`
  - Lines X-Y: [specific description of what changed]

## Technical Details
**Before:**
\`\`\`[language]
[actual code snippet showing the problem]
\`\`\`

**After:**
\`\`\`[language]
[actual code snippet showing the solution]
\`\`\`

[Brief explanation of why this solution works]
```

## Rules

- **File naming:** `YYYY-MM-DD-QUICKFIX-brief-description.md`
- Be thorough and technical — include code snippets, line numbers, root cause analysis
- For technical changes, always include:
  - Actual code snippets showing before/after
  - Specific line numbers or ranges
  - Root cause explanation with technical details
  - Quantified impact where measurable
- Apply precise writing standards — no weasel words, be direct
