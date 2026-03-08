---
name: weeklyReport
description: Create a stakeholder-ready weekly report from an approved weekly analysis — executive summary, key achievements, metrics, and FAQ section. Use after weeklyAnalysis has been reviewed and approved.
---
<!-- Version: 2026-03-08.1 -->

# Weekly Report

Create a stakeholder-ready weekly report from the approved weekly analysis.

## Process

1. Check that a weekly analysis file exists in `docs/weekly/`
2. If no analysis file is provided, look for the most recent `*-WEEKLY-ANALYSIS.md` file
3. Use the analysis classifications and ordering to create the report
4. Generate detailed FAQ answers by reading referenced sprint log files
5. Apply precise writing standards throughout

Write in an engaging, stakeholder-friendly style. Focus on business impact, user value, and measurable progress.

## Document Structure

```markdown
# Weekly Report: %start_date% — %end_date%

**Date:** %end_date%
**Task:** Weekly report covering [brief description of main themes]
**Category:** Documentation
**Status:** PUBLISHED

## Executive Summary
[2-3 paragraphs focusing on HIGH impact items from analysis. Lead with concrete accomplishments and quantified improvements.]

## Projects Focused On
[Brief overview of each project with activity, with insights into work done based on analysis classifications.]

## Key Achievements
[Detailed coverage of HIGH impact items and selected MEDIUM items. For each achievement, state WHAT was done, WHY it was done, and WHO it helps. Include specific metrics and business impact.]

## Technical Highlights
[Infrastructure improvements, performance optimizations, and architectural changes explained in business terms. Focus on measurable improvements and user benefits.]

## Challenges Overcome
[Problem-solving narratives from the analysis. Show technical expertise while explaining business impact of solutions.]

## Metrics & Impact
[Quantifiable improvements in bullet format:
- **Category**: Specific metric (before → after)
- Include percentages, time savings, system coverage, data volumes]

## Looking Ahead
[Future work from analysis: TODO items, in-progress implementations, and their expected business value.]

## Sprint Velocity
[Brief assessment of development pace, coordination between initiatives, and maintenance of platform stability during changes.]

---

## Questions You May Be Asking

[Generate 4-5 strategic questions based on the analysis content. For each:
1. Write the question focusing on business impact or technical decisions
2. Generate detailed answer by reading referenced sprint log files
3. Include specific technical details, metrics, and business context
4. Maintain file reference for transparency]

**Format:**
**1. [Question about major technical change or business impact]**

[Detailed answer with specific technical details, metrics, and business impact from referenced files]

*Reference: [filename from analysis]*

---

## References
This report was generated from: [YYYY-MM-DD-WEEKLY-ANALYSIS.md]
```

## Writing Standards

- **Remove weasel words entirely** — about, aim to, aligned, believe, could, essentially, feel, hope, seems, should, would, etc.
- **Quantify with specific numbers** — always, better, faster, more, significant, etc. require measurements
- **Use active voice** showing who performed the action
- **Replace subjective descriptors** with concrete examples and measurements
- **Eliminate hedge phrases** and disclaimers that weaken statements
- **Focus on measurable outcomes** and business impact

## Rules

- **File naming:** `YYYY-MM-DD-WEEKLY-REPORT.md` (using end date of week)
- The analysis must be reviewed and approved before generating this report
- Ask questions about strategic importance — not all activities carry equal weight
- Include the analysis filename in the References section
