# Incident Summary

## Scenario

An analyst or investigator needs to produce a concise executive summary from a single, detailed incident report. Common use cases include reducing a multi-page narrative to a one-page summary for leadership review, preparing case briefings for prosecutorial meetings, or creating condensed summaries for information sharing with partner agencies.

This template is designed for individual incident processing, not multi-incident analysis. For multi-incident work, see the [Situational Awareness Brief](situational-awareness-brief.md) or [Pattern Analysis](pattern-analysis.md) templates.

## Prerequisites

- **Complete incident report** including the primary narrative, supplemental reports, and officer statements.
- **CJI considerations** addressed. If working on a non-hardened system, ensure all CJI has been removed or redacted. See the [CJIS Compliance](../../docs/09-cjis-compliance.md) documentation for guidance.
- **Target audience** identified to calibrate the appropriate level of detail and terminology. A summary for a watch commander differs from one prepared for a prosecutorial briefing.
- **Desired word count** determined based on the audience and purpose.

## The Prompt

```
You are a law enforcement intelligence analyst preparing an incident
summary for supervisory review.

INCIDENT REPORT:
---
[PASTE FULL INCIDENT REPORT HERE]
---

Produce a concise incident summary with the following structure:

1. INCIDENT OVERVIEW
   - Case/report number (if provided)
   - Date, time, and location
   - Incident type/classification
   - Reporting officer/unit

2. NARRATIVE SUMMARY
   - What happened, in chronological order (3-5 sentences)
   - Key subjects involved (roles: victim, suspect, witness)
   - Actions taken by responding personnel

3. EVIDENCE AND DOCUMENTATION
   - Physical evidence collected or identified
   - Statements obtained
   - Digital evidence (surveillance, body camera, etc.)
   - Evidence gaps or items not yet collected

4. CURRENT STATUS
   - Case disposition (open, closed, pending)
   - Outstanding investigative actions
   - Pending lab results, warrants, or follow-up interviews

5. CONNECTIONS
   - Links to other cases, subjects, or patterns (if apparent)
   - Intelligence value for ongoing operations

CONSTRAINTS:
- Maximum 500 words for the entire summary
- Use only information present in the provided report
- Preserve exact dates, times, locations, and case numbers
- Do not editorialize or add opinions not present in the source
- Flag any contradictions or gaps in the source report
```

## Expected Output

A concise, structured summary of approximately 300-500 words organized into the five sections above. The summary should:

- Accurately capture the essential elements of the incident report
- Preserve key identifiers (case numbers, dates, times, locations) exactly as they appear in the source
- Be suitable for leadership review with minimal editing
- Clearly indicate any gaps or inconsistencies found in the source report

Expect the first draft to be approximately 85-90% usable for straightforward incident reports. More complex incidents with multiple subjects, locations, or timelines may require more significant editing.

## Review Checklist

- [ ] All dates, times, locations, and case numbers are accurate against the source
- [ ] No information was added beyond what the source report contains
- [ ] Chronology is correct and logically sequenced
- [ ] Subject roles (victim, suspect, witness) are correctly assigned
- [ ] Evidence items are accurately listed and categorized
- [ ] Case status is current and accurately reflected
- [ ] Summary fits within the target word count
- [ ] Language and terminology are appropriate for the target audience
- [ ] No CJI is present if the system is not hardened for CJI workloads
- [ ] Draft is marked as AI-assisted

---

*This template is part of the [NFCA Open Source LLM](../../README.md) companion resource. For prompting guidance, see the [Prompting Guide](../../docs/06-prompting-guide.md). For model selection, see the [Model Selection Guide](../../docs/05-model-selection.md).*
