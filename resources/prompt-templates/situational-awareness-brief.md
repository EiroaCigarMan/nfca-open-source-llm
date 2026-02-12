# Situational Awareness Brief

## Scenario

An analyst needs to compile a situational awareness brief from multiple incident reports covering a defined time period. Common use cases include Monday morning briefs from weekend activity, shift change briefs, and periodic operational summaries for supervisory review.

This template is designed for daily or periodic operational briefings where multiple incidents need to be synthesized into a single, actionable product.

## Prerequisites

- **Incident reports** for the time period (typically from CAD/RMS export or manual compilation). All reports should cover the same geographic area and time window.
- **CJI considerations** addressed. If working on a non-hardened system, ensure all CJI has been removed or redacted before pasting into the prompt. See the [CJIS Compliance](../../docs/09-cjis-compliance.md) documentation for guidance.
- **Target audience** identified (supervisory staff, watch commanders, partner agencies, etc.) to calibrate the appropriate level of detail.
- **Baseline data** (optional) -- prior period statistics or averages for comparison.

## The Prompt

```
You are a law enforcement intelligence analyst preparing a situational
awareness brief for supervisory review.

TIME PERIOD: [e.g., Friday 1800 through Monday 0600]
JURISDICTION: [e.g., the reporting area]

The following incident reports were filed during this period:

---
[PASTE INCIDENT REPORT 1]
---
[PASTE INCIDENT REPORT 2]
---
[PASTE INCIDENT REPORT 3]
---
[ADD MORE AS NEEDED]
---

Produce a situational awareness brief with the following sections:

1. EXECUTIVE SUMMARY
   - 3-5 sentences covering the most significant activity
   - Total incident count and breakdown by category
   - Any notable trends compared to typical activity levels

2. SIGNIFICANT INCIDENTS
   - One paragraph per significant incident
   - Include: date/time, location, nature of incident, current status
   - Prioritize by severity and operational impact

3. CRIME PATTERNS AND TRENDS
   - Any geographic clusters or repeat locations
   - Temporal patterns (time of day, day of week)
   - Connections to ongoing investigations or known subjects
   - Comparison to recent baseline activity

4. INFORMATION GAPS
   - Reports with incomplete information
   - Conflicting details across reports
   - Areas where additional information is needed

5. RECOMMENDED ACTIONS
   - Specific follow-up steps for each significant incident
   - Resource allocation suggestions based on identified patterns
   - Intelligence requirements for the upcoming period

CONSTRAINTS:
- Base all content strictly on the provided reports
- Clearly distinguish between confirmed facts and analytical assessments
- Use confidence language for any analytical judgments
- Do not include information not present in the source reports
- Flag any data quality issues in the source material
```

## Expected Output

A structured 1-3 page brief organized by the five sections above:

- **Executive Summary** should be immediately usable with minimal editing
- **Significant Incidents** should accurately reflect the source reports in chronological or priority order
- **Crime Patterns and Trends** will require analyst review and validation -- the model may identify patterns that are coincidental or miss patterns that require domain knowledge
- **Information Gaps** should highlight genuine deficiencies in the source data
- **Recommended Actions** should be treated as suggestions requiring analyst judgment

Overall, expect the first draft to be approximately 80% usable, with the remaining 20% requiring analyst judgment, local knowledge, and verification against original sources.

## Review Checklist

- [ ] All facts in the brief trace back to a specific source report
- [ ] No information was fabricated or inferred beyond the provided data
- [ ] Confidence language is used appropriately for analytical assessments
- [ ] Geographic and temporal details are accurate (verify addresses, dates, times)
- [ ] Incident categorization is correct per agency standards
- [ ] Recommended actions are actionable, appropriate, and within policy
- [ ] Brief follows established agency format and standards
- [ ] Draft is marked as AI-assisted and requires human review before dissemination
- [ ] No CJI is present if the system is not hardened for CJI workloads

---

*This template is part of the [NFCA Open Source LLM](../../README.md) companion resource. For prompting guidance, see the [Prompting Guide](../../docs/06-prompting-guide.md). For model selection, see the [Model Selection Guide](../../docs/05-model-selection.md).*
