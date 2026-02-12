# Evidence Review

## Scenario

An analyst or investigator needs to synthesize evidence collected across multiple reports, identify gaps in the evidence chain, and produce a structured review suitable for case file documentation or prosecution preparation. Common use cases include pre-trial evidence organization, case file review for completeness, quality assurance audits, and preparation for prosecutorial meetings.

This template organizes evidence documentation into a structured format that highlights what has been collected, what is pending, and what is missing. It is designed to support investigative completeness rather than to replace legal analysis of evidence admissibility.

## Prerequisites

- **Evidence logs and property receipts** -- documentation of all items collected, including item numbers, descriptions, dates, and collecting personnel.
- **Chain of custody documentation** -- transfer records for evidence items, lab submissions, and returns.
- **Related incident reports and supplemental reports** -- the full investigative record for context.
- **Lab results** or pending analysis status (if available) -- forensic findings and outstanding submissions.
- **Case theory or investigative hypothesis** (optional but helpful) -- provides the model with context about what evidence types would typically be expected, improving gap identification.
- **CJI considerations** addressed. Case evidence documentation may contain CJI. See the [CJIS Compliance](../../docs/09-cjis-compliance.md) documentation for guidance.

## The Prompt

```
You are a law enforcement intelligence analyst conducting an evidence
review for case file documentation.

CASE INFORMATION:
- Case Number: [CASE NUMBER]
- Offense(s): [CHARGES/OFFENSES]
- Date of Incident: [DATE]

EVIDENCE DOCUMENTATION:
---
[PASTE EVIDENCE LOGS, PROPERTY RECEIPTS, AND RELATED REPORTS]
---

Produce an evidence review with the following sections:

1. EVIDENCE INVENTORY
   - Complete list of all evidence items referenced in the provided
     documentation
   - For each item: item number, description, date/time collected,
     location collected, collecting officer/unit, current status
     (in custody, sent to lab, returned, etc.)

2. CHAIN OF CUSTODY ASSESSMENT
   - For each evidence item, note whether chain of custody
     documentation appears complete
   - Flag any gaps in custody documentation
   - Identify items where custody transfers are not documented

3. FORENSIC ANALYSIS STATUS
   - Items submitted for lab analysis and current status
   - Results received and summary of findings
   - Items pending analysis with expected timelines
   - Items that should be submitted but have not been

4. EVIDENCE GAPS
   - Evidence types that would typically be expected for this
     offense type but are not present
   - Potential evidence sources not yet explored (surveillance,
     digital records, additional witnesses)
   - Documentation gaps that could affect admissibility

5. EVIDENCE CONNECTIONS
   - How evidence items corroborate or contradict witness statements
   - Physical evidence that links subjects to locations or events
   - Temporal analysis of evidence (timeline support)

6. RECOMMENDATIONS
   - Additional evidence collection steps
   - Items requiring expedited lab processing
   - Documentation that needs to be completed
   - Potential admissibility concerns to address

CONSTRAINTS:
- Work only from the provided documentation
- Do not assume the existence of evidence not mentioned in the source
- Flag contradictions between evidence items and witness statements
- Use specific item numbers and dates when referencing evidence
- Note any procedural concerns observed in the documentation
- This review is a draft requiring investigator validation
```

## Expected Output

A structured evidence review document of 2-4 pages organized by the six sections above:

- **Evidence Inventory** should be comprehensive and accurately reflect the source documentation, formatted as a list or table with consistent fields
- **Chain of Custody Assessment** should note both complete and incomplete custody chains, with specific items flagged
- **Forensic Analysis Status** should accurately reflect what has been submitted, what is pending, and what has been returned with results
- **Evidence Gaps** will require investigator review -- the model may identify gaps that are not actually relevant to the specific case theory, or miss gaps that require domain expertise
- **Evidence Connections** provides a starting framework for evidence synthesis but should not be treated as legal analysis
- **Recommendations** should be actionable and specific

Expect the evidence inventory and forensic status sections to be highly accurate (directly transcribed from source data). The gap analysis and recommendations sections require more analyst judgment and may include suggestions that are not relevant to the specific case.

## Review Checklist

- [ ] Evidence inventory is complete and accurate against source documentation
- [ ] Item numbers, dates, and descriptions are correctly transcribed
- [ ] No evidence items were fabricated by the model
- [ ] Chain of custody observations are accurate and specific
- [ ] Lab analysis status is current and correctly categorized
- [ ] Identified gaps are legitimate and relevant to the offense type
- [ ] Recommendations are actionable, appropriate, and legally sound
- [ ] Procedural concerns cited are based on documented facts, not assumptions
- [ ] Evidence connections are supported by the provided documentation
- [ ] No legal conclusions are drawn (this is an organizational tool, not legal analysis)
- [ ] Draft is marked as AI-assisted and requires investigator validation
- [ ] No CJI is present if the system is not hardened for CJI workloads

---

*This template is part of the [NFCA Open Source LLM](../../README.md) companion resource. For prompting guidance, see the [Prompting Guide](../../docs/06-prompting-guide.md). For model selection, see the [Model Selection Guide](../../docs/05-model-selection.md).*
