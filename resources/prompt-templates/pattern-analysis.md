# Pattern Analysis

## Scenario

An analyst needs to identify patterns, connections, or trends across multiple cases, incidents, or reports. Common use cases include serial offense analysis, geographic crime clustering, temporal trend identification, modus operandi comparison, and subject linkage across jurisdictions.

This template supports multi-dimensional analysis: temporal patterns (when offenses occur), geographic patterns (where they cluster), behavioral patterns (how they are committed), and subject linkage (who may be connected). It is designed for analytical products that inform tactical operations, resource allocation, and investigative strategy.

Pattern analysis is one of the areas where local LLMs provide the most value -- and also one of the areas where critical review is most important. The model may identify coincidental correlations as patterns or miss patterns that require local knowledge. Every identified pattern should be treated as a hypothesis until validated by an analyst with domain expertise.

## Prerequisites

- **Multiple incident reports, case summaries, or intelligence reports** covering the analysis period. A minimum of 5-10 incidents is recommended for meaningful pattern identification; fewer incidents increase the risk of identifying coincidental correlations.
- **Clear analysis scope** defined: geographic area, time period, crime type(s), and analysis purpose.
- **Baseline data** for comparison if available (e.g., same period last year, rolling monthly averages, national or regional trends). Baseline comparison significantly improves the quality of trend identification.
- **CJI considerations** addressed. Multi-case analysis may involve aggregation of CJI across multiple reports. See the [CJIS Compliance](../../docs/09-cjis-compliance.md) documentation for guidance.
- **Source data formatted consistently** -- if incident reports come from multiple systems, standardize the key fields (date, time, location, offense type) before pasting into the prompt.

## The Prompt

```
You are a law enforcement intelligence analyst conducting a pattern
analysis across multiple incidents.

ANALYSIS PARAMETERS:
- Geographic scope: [AREA/JURISDICTION]
- Time period: [START DATE] through [END DATE]
- Crime type(s): [OFFENSE CATEGORIES]
- Analysis purpose: [e.g., identify serial offender patterns,
  geographic clustering, temporal trends]

SOURCE DATA:
---
[PASTE INCIDENT REPORTS, CASE SUMMARIES, OR DATA EXTRACTS]
---

BASELINE COMPARISON (if available):
---
[PASTE COMPARATIVE DATA -- previous period, annual averages, etc.]
---

Produce a pattern analysis with the following sections:

1. ANALYSIS SUMMARY
   - Total incidents reviewed
   - Key findings in 3-5 bullet points
   - Confidence level in identified patterns (high/moderate/low)

2. TEMPORAL ANALYSIS
   - Day-of-week distribution
   - Time-of-day patterns
   - Trend over the analysis period (increasing, decreasing, stable)
   - Comparison to baseline if provided

3. GEOGRAPHIC ANALYSIS
   - Location clustering (repeat addresses or areas)
   - Geographic progression or movement patterns
   - Proximity to known locations of interest

4. MODUS OPERANDI ANALYSIS
   - Common methods, tools, or approaches across incidents
   - Entry/exit patterns (for property crimes)
   - Target selection patterns (victim or location types)
   - Unique signatures or distinguishing behaviors

5. SUBJECT LINKAGE
   - Physical descriptions or identifiers that appear across
     multiple incidents
   - Vehicle descriptions or license plates appearing in
     multiple reports
   - Communication indicators (phone numbers, online accounts)
   - Known associates or common locations

6. ASSESSMENT
   - Likelihood of linked incidents (with supporting evidence)
   - Predicted next occurrence (based on identified patterns)
   - Alternative hypotheses for observed patterns
   - Limitations of this analysis

7. RECOMMENDED ACTIONS
   - Investigative steps to confirm or refute patterns
   - Tactical recommendations (patrol emphasis, surveillance)
   - Intelligence collection priorities
   - Information sharing recommendations

CONSTRAINTS:
- Base all pattern identification on documented evidence
- Clearly distinguish between patterns supported by evidence and
  hypotheses requiring further investigation
- Use specific incident numbers and dates when citing evidence
- Acknowledge alternative explanations for observed patterns
- Rate confidence levels for each identified pattern
- This analysis is a draft requiring analyst validation
```

## Expected Output

A structured analytical product of 3-5 pages organized by the seven sections above:

- **Analysis Summary** should provide an accurate overview with appropriately rated confidence levels
- **Temporal Analysis** should accurately reflect the day/time distribution from the source data -- verify counts against the actual reports
- **Geographic Analysis** will identify location clusters from the provided addresses; accuracy depends on the quality of location data in the source reports
- **Modus Operandi Analysis** is where the model provides the most analytical value -- identifying behavioral similarities that an analyst might miss in manual review. However, this is also where hallucination risk is highest. Verify every claimed similarity against the source reports.
- **Subject Linkage** should reference specific descriptors from the source data. Be especially cautious of the model inferring connections from partial or similar descriptions
- **Assessment** should include alternative hypotheses and explicit limitations. If the model presents only one explanation, add alternatives during review
- **Recommended Actions** should be treated as suggestions -- validate for operational appropriateness, resource availability, and policy compliance

Pattern analysis is one of the highest-value use cases for local LLMs, but also requires the most rigorous review. Expect to spend 20-30 minutes reviewing and refining the model's output for a 10-incident analysis.

## Review Checklist

- [ ] All cited incidents, dates, and times are accurate against source data
- [ ] Identified patterns are supported by documented evidence (not fabricated correlations)
- [ ] Temporal counts and distributions are mathematically correct
- [ ] Geographic clusters are verified against actual addresses
- [ ] MO similarities reference specific incidents and are verifiable
- [ ] Subject linkages are based on documented descriptors, not assumptions
- [ ] Confidence levels are appropriately assigned (not uniformly "high")
- [ ] Alternative explanations are considered for each identified pattern
- [ ] Correlation is not presented as causation
- [ ] Predictions are clearly labeled as analytical assessments, not certainties
- [ ] Recommendations are actionable, proportionate, and within policy
- [ ] Analysis limitations are honestly stated
- [ ] No patterns were fabricated from insufficient data points
- [ ] Draft is marked as AI-assisted and requires analyst validation
- [ ] No CJI is present if the system is not hardened for CJI workloads

---

*This template is part of the [NFCA Open Source LLM](../../README.md) companion resource. For prompting guidance, see the [Prompting Guide](../../docs/06-prompting-guide.md). For model selection, see the [Model Selection Guide](../../docs/05-model-selection.md). For understanding model limitations that affect pattern analysis, see [Model Selection -- What These Models Are Bad At](../../docs/05-model-selection.md#what-these-models-are-bad-at).*
