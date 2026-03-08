---
name: weeklyAnalysis
description: Create a weekly analysis by scanning and classifying development activities across projects by impact level — HIGH, MEDIUM, LOW, MAINTENANCE, FUTURE. Use on Fridays or end-of-week to prepare for the weekly report.
---
<!-- Version: 2026-03-08.1 -->

# Weekly Analysis

Consolidate and classify development activities across all projects for the week.

## Process

1. Run `date` to get current date — format as YYYY-MM-DD
2. Ask to confirm today's date and the week's date range to analyze
3. Scan project `docs/` directories for files matching the date range:
   - `docs/daily/` — UPDATE and QUICKFIX files
   - `docs/todo/` — TODO files (including completed subfolder)
   - `docs/weekly/` — Previous reports for context
   - `docs/working/` — IMPLEMENTATION files (including completed subfolder)
4. Read all identified files and classify activities by impact
5. Create analysis document in the current project's `docs/weekly/` folder

The week typically runs Saturday through Friday. Include weekend activity.

## Document Structure

```markdown
# Weekly Development Analysis: %start_date% — %end_date%

**Date:** %end_date%
**Purpose:** Impact classification and prioritization for weekly report
**Category:** Documentation
**Status:** REVIEWED
**Projects Covered:** [list all projects with activity]

---

## HIGH IMPACT / STRATEGIC IMPORTANCE

### [Activity Name] ([Project]) — [IN PROGRESS if applicable]
**Files:** [list relevant files]
**Impact:** [what changed technically]
**Business Value:** [why it matters to stakeholders]
**Stakeholder Benefit:** [who benefits and how]

## MEDIUM IMPACT / FEATURE ENHANCEMENTS
[Same format]

## LOW IMPACT / OPERATIONAL IMPROVEMENTS
[Same format]

## MAINTENANCE / BUG FIXES
[Same format]

## FUTURE WORK / PLANNING

### [Activity Name] — [TODO/IN PROGRESS status]
**Files:** [list relevant files]
**Impact:** [what this would accomplish]
**Business Value:** [potential value when implemented]
**Status:** [current status and next steps]

---

## RECOMMENDED ORDERING FOR REPORT

**Review Instructions:**
1. Do you agree with the impact classifications?
2. Should any items move between categories?
3. Are there activities to exclude from the report?
4. What order would you prefer within each category?

**Suggested Report Structure:**
1. Executive Summary (focus on HIGH impact items)
2. Key Achievements (HIGH + selected MEDIUM)
3. Technical Highlights (infrastructure, performance gains)
4. Challenges Overcome (problem-solving narratives)
5. Metrics & Impact (quantifiable improvements)
6. Looking Ahead (TODO items and in-progress work)
```

## Impact Classification Guidelines

- **HIGH**: Strategic initiatives, major performance improvements (>50% gains), infrastructure changes affecting multiple systems, new automation capabilities
- **MEDIUM**: Feature enhancements, user experience improvements, API consolidations, significant bug fixes affecting workflows
- **LOW**: Operational improvements, workflow optimizations, developer productivity enhancements, minor UI improvements
- **MAINTENANCE**: Bug fixes, quick fixes, compatibility updates, formatting cleanup, dependency updates
- **FUTURE**: TODO items, planning documents, in-progress implementations not yet delivering value

## Classification Criteria

- Consider business impact over technical complexity
- Flag IN PROGRESS items that span multiple weeks
- Group related activities
- Prioritize user-facing improvements over internal tooling

## Rules

- **File naming:** `YYYY-MM-DD-WEEKLY-ANALYSIS.md` (using end date of week)
- Ask questions — not all activities carry equal value. Let the user help discern strategic importance.
- Present the analysis for review BEFORE creating the weekly report
