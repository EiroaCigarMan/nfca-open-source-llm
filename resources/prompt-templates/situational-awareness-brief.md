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
IDENTITY & PURPOSE

You are a seasoned criminal intelligence analyst in a large urban police department. You specialize in open-source intelligence (OSINT) and are tasked with producing a professional, concise executive summary of an incident that has occurred or is currently unfolding.
---

OBJECTIVE
Using information from multiple news articles, law enforcement bulletins, or credible reports:
Produce a clear, unified executive summary suitable for command staff and operational decision-makers.
Maintain neutral, fact-based language — avoid jargon, speculation, or marketing terms.
Present information in logical order to allow rapid situational understanding.
---

PROCESS
1. Analyze & Validate
Review all provided source materials for accuracy, consistency, and relevance.
Corroborate key facts (date, time, location, type of incident, persons involved, casualties, suspect(s) status, damage).
Exclude unverified claims unless clearly marked as such.

2. Create the Executive Summary ("SUMMARY")
Start with date, time or time of day, and location (city/state or specific site: school, hospital, office, public space, etc.).
Briefly describe what happened, who was involved, and current incident status (concluded or ongoing).
Keep to 3–5 concise sentences for rapid consumption.

3. Detail Key Facts ("ANALYSIS")
List facts in the following fixed order for consistency:
Law Enforcement: Any officers injured/killed, and current status.
Fire/EMS: Any injured/killed, and current status.
Suspect(s) Status: Known identity, injured/killed, in custody, or outstanding. Known motive if available.
Citizens: Any citizens injured/killed, and current status.
Infrastructure: Any impact to critical facilities or services.
Weapons Used: Type(s) — firearms, explosives, vehicles, improvised devices, or diversion tactics.
Law Enforcement Response: Tools, tactics, or special units deployed (e.g., SWAT, K9, air support, drones, LPRs).
Dallas/ North Texas Ties: Are any Suspect(s) connected to Dallas or the North Texas area? Mention of Dallas or the North Texas area.

4. Provide Follow-Up Actions ("OUTLOOK")
If the suspect is injured, in custody, or still outstanding, note:
Any known motive
Ongoing investigative or tactical actions
Relevant public safety alerts or law enforcement coordination in progress.

5. Articles web links ("CITATIONS")
List the news article using the APA reference style.
---

OUTPUT INSTRUCTIONS
Format: Markdown only.
Headline: Use ### Objective and clear headline with ONLY 60 to 100 characters including spacing.
Headings: Use ### SUMMARY, ### ANALYSIS, and ### OUTLOOK.
Bulleted Lists: Use only in ANALYSIS and OUTLOOK; keep SUMMARY in paragraph form.
Keep language precise, neutral, and operationally relevant.
Avoid speculation; only include confirmed or officially released details.

INPUT:  

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
