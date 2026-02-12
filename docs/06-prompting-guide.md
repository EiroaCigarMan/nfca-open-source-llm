# Prompt Engineering for Law Enforcement Workflows

> **Summary:** Effective prompting is the difference between an AI tool that saves hours and one that wastes them. This guide covers prompt structure, system prompts, Ollama Modelfiles, and provides full copy-paste-ready templates for common law enforcement analytical tasks. The core principle is simple: practitioners already know how to do their jobs -- effective prompting means writing those steps down in a way the model can follow.

---

## Table of Contents

- [Core Principle](#core-principle)
- [Prompt Structure Framework](#prompt-structure-framework)
- [System Prompts vs. User Prompts](#system-prompts-vs-user-prompts)
- [Domain-Specific Prompt Templates](#domain-specific-prompt-templates)
  - [Situational Awareness Brief](#template-1-situational-awareness-brief)
  - [Incident Summary](#template-2-incident-summary)
  - [BOLO Generation](#template-3-bolo-generation)
  - [Evidence Review](#template-4-evidence-review)
  - [Pattern Analysis](#template-5-pattern-analysis)
- [Prompt Libraries and Sharing](#prompt-libraries-and-sharing)
- [Common Prompting Mistakes in Law Enforcement Context](#common-prompting-mistakes-in-law-enforcement-context)
- [References](#references)

---

## Core Principle

> **"You already know how to do your job -- now write those steps for AI."**

A common misconception is that prompting requires a specialized skill set. In practice, the most effective prompts are written by domain experts who translate their existing expertise into clear instructions. An analyst who has written hundreds of intelligence briefings already knows the structure, the required elements, and the quality standards. Effective prompting means encoding that knowledge into a prompt the model can follow.

Consider how a senior analyst might train a new analyst:

1. "Here is the role you are filling" (Role)
2. "Here is the data you are working with" (Context)
3. "Here is exactly what I need you to produce" (Task)
4. "Here is the format it should follow" (Output Format)
5. "Here are the things to avoid and the things to prioritize" (Constraints)

That training conversation **is** a prompt. The difference is that it is being delivered to a model instead of a person.

---

## Prompt Structure Framework

Every effective prompt has five components. Not every prompt needs all five, but more structure consistently produces better results.

### The Five Components

| Component | Purpose | Example |
|-----------|---------|---------|
| **Role** | Define the AI's perspective and expertise | "You are a law enforcement intelligence analyst..." |
| **Context** | Provide the data or background the model needs | "The following are three incident reports from the past 72 hours..." |
| **Task** | State exactly what output is needed | "Produce a situational awareness brief covering..." |
| **Output Format** | Specify the structure of the response | "Structure the brief with these sections: Executive Summary, Key Incidents, Patterns, Recommended Actions" |
| **Constraints** | Define boundaries and priorities | "Do not speculate beyond the provided data. Flag any gaps in the source material." |

### Progressive Complexity

Start simple and add structure as needed. Three examples showing how the same request improves with additional structure:

**Weak prompt (too vague):**
```
Summarize these incidents.
```

**Better prompt (adds task and format):**
```
Summarize the following three incident reports into a 200-word executive summary.
Focus on: what happened, where, when, and who was involved.
```

**Strong prompt (all five components):**
```
You are a law enforcement intelligence analyst preparing a daily brief for
supervisory review.

The following three incident reports were filed in the past 24 hours:

[PASTE INCIDENT REPORTS HERE]

Produce a daily intelligence brief with the following sections:
1. Executive Summary (3-5 sentences covering the most significant developments)
2. Incident Summaries (one paragraph per incident: what, where, when, who)
3. Connections and Patterns (any links between incidents or to ongoing cases)
4. Information Gaps (what is missing or unconfirmed)
5. Recommended Actions (suggested follow-up steps)

Constraints:
- Base all analysis strictly on the provided reports
- Clearly distinguish between confirmed facts and analytical assessments
- Flag any information that appears inconsistent across reports
- Do not include information not present in the source material
```

The strong prompt produces output that requires less editing because the model has clear instructions about structure, content, and boundaries.

---

## System Prompts vs. User Prompts

### What Is a System Prompt?

A **system prompt** is a set of persistent instructions that define the model's behavior for every interaction. It establishes the model's role, default constraints, and behavioral guidelines. Think of it as standing orders.

A **user prompt** is the specific request made in each interaction. It changes with every query.

```
System Prompt (set once, applies to all interactions):
  "You are a law enforcement intelligence analyst. Always structure
   outputs with clear headers. Never fabricate information. Flag
   uncertainty explicitly."

User Prompt (changes each time):
  "Summarize the following incident report..."
```

### Why System Prompts Matter

Without a system prompt, the model defaults to generic behavior. With a system prompt tailored to law enforcement analytical work, the model:

- Uses appropriate terminology and tone
- Follows consistent output structures
- Applies relevant constraints automatically (no fabrication, flag uncertainty)
- Reduces the need to repeat instructions in every prompt

### Using Ollama Modelfiles to Save System Prompts

Ollama supports **Modelfiles** -- configuration files that bundle a base model with a custom system prompt, temperature settings, and other parameters. This allows saving and sharing standardized model configurations.

**Creating a Modelfile:**

Create a text file named `Modelfile` (no extension) with the following structure:

```dockerfile
FROM llama3.1:8b

PARAMETER temperature 0.3
PARAMETER num_ctx 8192

SYSTEM """
You are a law enforcement intelligence analyst assigned to a fusion center.
Your role is to produce analytical products from provided source material.

Standing instructions:
- All analysis must be grounded in the provided source material
- Clearly distinguish between confirmed facts and analytical assessments
- Use confidence language (likely, possibly, insufficient evidence) for assessments
- Flag information gaps and inconsistencies
- Structure all outputs with clear headers and sections
- Never fabricate information or speculate beyond the evidence
- Use professional law enforcement terminology
- All outputs are drafts requiring human review before dissemination
"""
```

**Building and using the custom model:**

```bash
# Create the custom model
ollama create le-analyst -f Modelfile

# Run it
ollama run le-analyst "Summarize the following report..."

# The system prompt applies automatically to every interaction
```

**Explanation of parameters:**

| Parameter | Value | Purpose |
|-----------|-------|---------|
| `FROM` | `llama3.1:8b` | Base model to use (see [Model Selection](05-model-selection.md)) |
| `temperature` | `0.3` | Lower temperature produces more consistent, focused outputs. See note below. |
| `num_ctx` | `8192` | Context window size in tokens. Increase for longer documents (uses more VRAM). |
| `SYSTEM` | (text) | System prompt applied to every interaction |

**Important note on temperature:** Setting temperature to 0 or a low value (0.1-0.3) makes outputs more *consistent and repeatable*. It does **not** make them more *accurate or truthful*. The model can still hallucinate at temperature 0. Low temperature is recommended for analytical work because consistency matters -- the same input should produce similar output each time. But human review remains mandatory regardless of temperature setting. See [Understanding LLMs](02-understanding-llms.md) for a deeper explanation.

### Multiple Modelfiles for Different Tasks

Create specialized Modelfiles for different analytical functions:

```bash
# Analyst briefing model
ollama create le-analyst -f Modelfile-analyst

# BOLO drafting model
ollama create le-bolo -f Modelfile-bolo

# Evidence review model
ollama create le-evidence -f Modelfile-evidence

# Switch between them as needed
ollama run le-analyst "Prepare a brief from..."
ollama run le-bolo "Draft a BOLO for..."
```

---

## Domain-Specific Prompt Templates

The following templates are ready to copy, paste, and adapt. Each template is also available as a standalone file in the [`resources/prompt-templates/`](../resources/prompt-templates/) directory for easy reference.

**Before using any template:**
1. Replace all placeholder text (marked with brackets) with actual data
2. Review the prerequisites section to ensure the necessary inputs are prepared
3. After receiving output, complete the review checklist before using the result

---

### Template 1: Situational Awareness Brief

**Scenario:** An analyst needs to compile a situational awareness brief from multiple incident reports covering a defined time period (e.g., weekend activity for a Monday morning brief).

**Prerequisites:**
- Collected incident reports for the time period (typically from CAD/RMS export or manual compilation)
- Removed or redacted any CJI if working on a non-hardened system (see [CJIS Compliance](09-cjis-compliance.md))
- Identified the target audience (supervisory staff, watch commanders, etc.)

**The Prompt:**

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

**Expected Output:** A structured 1-3 page brief organized by the five sections above, with each section populated from the provided incident data. The Executive Summary should be immediately usable. Pattern and trend sections will require analyst review and validation before dissemination.

**Review Checklist:**
- [ ] All facts in the brief trace back to a specific source report
- [ ] No information was fabricated or inferred beyond the data
- [ ] Confidence language is used appropriately for analytical assessments
- [ ] Geographic and temporal details are accurate
- [ ] Incident categorization is correct
- [ ] Recommended actions are actionable and appropriate
- [ ] Brief follows established agency format and standards
- [ ] Draft is marked as AI-assisted and requires human review

> **Standalone template:** [`resources/prompt-templates/situational-awareness-brief.md`](../resources/prompt-templates/situational-awareness-brief.md)

---

### Template 2: Incident Summary

**Scenario:** An analyst needs to produce a concise executive summary from a single, detailed incident report -- for example, reducing a multi-page narrative to a one-page summary for leadership review.

**Prerequisites:**
- Complete incident report (narrative, supplemental reports, officer statements)
- Identified audience and required level of detail
- CJI considerations addressed per [CJIS Compliance](09-cjis-compliance.md)

**The Prompt:**

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

**Expected Output:** A concise, structured summary of approximately 300-500 words that captures the essential elements of the incident report. The summary should be suitable for leadership review with minimal editing.

**Review Checklist:**
- [ ] All dates, times, locations, and case numbers are accurate
- [ ] No information was added beyond what the source report contains
- [ ] Chronology is correct and logically sequenced
- [ ] Evidence items are accurately listed
- [ ] Case status is current and accurate
- [ ] Summary fits within the target word count
- [ ] Language is appropriate for the target audience

> **Standalone template:** [`resources/prompt-templates/incident-summary.md`](../resources/prompt-templates/incident-summary.md)

---

### Template 3: BOLO Generation

**Scenario:** An analyst needs to draft a Be On the Lookout (BOLO) bulletin from available suspect and vehicle descriptions, incident details, and operational context.

**Prerequisites:**
- Suspect description(s) from witness statements, surveillance, or officer reports
- Vehicle information if applicable
- Incident summary with relevant details
- Distribution list and urgency level determined

**The Prompt:**

```
You are a law enforcement intelligence analyst drafting a BOLO
(Be On the Lookout) bulletin for distribution to patrol units
and neighboring agencies.

SOURCE INFORMATION:
---
[PASTE SUSPECT DESCRIPTION, VEHICLE INFO, AND INCIDENT DETAILS]
---

Produce a BOLO bulletin with the following structure:

1. HEADER
   - BOLO number: [ASSIGN OR LEAVE BLANK]
   - Date/time issued
   - Issuing unit
   - Priority level: [ROUTINE / PRIORITY / URGENT]
   - Expiration: [DATE]

2. SUBJECT DESCRIPTION
   - Physical description (sex, race, age range, height, weight,
     hair, eyes, distinguishing features)
   - Clothing description at time of incident
   - Known aliases or identifiers
   - NOTE: Clearly indicate which descriptors are confirmed vs.
     approximate

3. VEHICLE DESCRIPTION (if applicable)
   - Year, make, model, color
   - License plate (full or partial)
   - Distinguishing features (damage, stickers, modifications)
   - Direction of travel and last known location

4. INCIDENT SUMMARY
   - Brief description of the incident (2-3 sentences)
   - Date, time, and location of occurrence
   - Charges or offenses involved

5. OFFICER SAFETY INFORMATION
   - Known weapons involvement
   - History of violence or resistance
   - Mental health indicators
   - Other safety considerations

6. ACTION REQUESTED
   - What officers should do upon contact
   - Contact information for the investigating unit
   - Case number for reference

CONSTRAINTS:
- Use only confirmed information from the provided source material
- Clearly mark any approximate or unconfirmed details with
  "APPROXIMATE" or "UNCONFIRMED"
- Do not add physical descriptions or details not in the source
- Prioritize officer safety information
- Keep language clear, concise, and actionable
- Total length should not exceed one page
```

**Expected Output:** A one-page BOLO bulletin formatted for immediate distribution. The bulletin should contain only information from the provided source material, with approximate or unconfirmed details clearly marked.

**Review Checklist:**
- [ ] All physical descriptions match the source material exactly
- [ ] No descriptors were fabricated or assumed by the model
- [ ] Approximate details are clearly marked
- [ ] Vehicle information is accurate (plate, make, model, color)
- [ ] Officer safety information is complete and accurate
- [ ] Contact information and case numbers are correct
- [ ] Bulletin does not exceed one page
- [ ] Priority level is appropriate for the incident
- [ ] Expiration date is set appropriately

> **Standalone template:** [`resources/prompt-templates/bolo-generator.md`](../resources/prompt-templates/bolo-generator.md)

---

### Template 4: Evidence Review

**Scenario:** An analyst or investigator needs to synthesize evidence collected across multiple reports, identify gaps in the evidence chain, and produce a structured review suitable for case file documentation or prosecution preparation.

**Prerequisites:**
- Evidence logs, property receipts, or chain of custody documentation
- Related incident reports and supplemental reports
- Lab results or pending analysis status (if available)
- Case theory or investigative hypothesis (optional but helpful)

**The Prompt:**

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

**Expected Output:** A structured evidence review document of 2-4 pages, organized by the six sections above. The evidence inventory should be comprehensive and accurately reflect the source documentation. Gap analysis and recommendations should be clearly tied to specific evidence items.

**Review Checklist:**
- [ ] Evidence inventory is complete and accurate against source documentation
- [ ] Item numbers, dates, and descriptions are correctly transcribed
- [ ] Chain of custody observations are accurate
- [ ] Lab analysis status is current and correct
- [ ] Identified gaps are legitimate (not based on fabricated expectations)
- [ ] Recommendations are actionable and appropriate
- [ ] No evidence items were fabricated by the model
- [ ] Procedural concerns cited are based on documented facts

> **Standalone template:** [`resources/prompt-templates/evidence-review.md`](../resources/prompt-templates/evidence-review.md)

---

### Template 5: Pattern Analysis

**Scenario:** An analyst needs to identify patterns, connections, or trends across multiple cases, incidents, or reports. This template supports temporal analysis, geographic clustering, subject linkage, and modus operandi comparison.

**Prerequisites:**
- Multiple incident reports, case summaries, or intelligence reports covering the analysis period
- Clear definition of the analysis scope (geographic area, time period, crime type)
- Baseline data for comparison if available (e.g., same period last year, monthly averages)

**The Prompt:**

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

**Expected Output:** A structured analytical product of 3-5 pages identifying patterns, connections, and trends across the provided incidents. The analysis should clearly distinguish between evidence-supported patterns and hypotheses, include confidence levels, and provide actionable recommendations.

**Review Checklist:**
- [ ] All cited incidents and dates are accurate
- [ ] Identified patterns are supported by the provided data
- [ ] Confidence levels are appropriately assigned
- [ ] Alternative explanations are considered
- [ ] Geographic and temporal claims are verifiable against source data
- [ ] Recommendations are actionable and proportionate
- [ ] Analysis distinguishes between correlation and causation
- [ ] No patterns were fabricated from insufficient data
- [ ] Draft is marked as AI-assisted and requires analyst validation

> **Standalone template:** [`resources/prompt-templates/pattern-analysis.md`](../resources/prompt-templates/pattern-analysis.md)

---

## Prompt Libraries and Sharing

### Saving Prompts as Modelfiles

The most efficient way to share and reuse prompts is through Ollama Modelfiles. A Modelfile bundles the base model, system prompt, and parameters into a single, reproducible configuration.

**Step 1: Create Modelfile for each workflow**

```bash
# Create a directory for Modelfiles
mkdir -p ~/ollama-modelfiles

# Create a Modelfile for the analyst role
cat > ~/ollama-modelfiles/Modelfile-analyst << 'EOF'
FROM llama3.1:8b

PARAMETER temperature 0.3
PARAMETER num_ctx 8192

SYSTEM """
You are a law enforcement intelligence analyst assigned to a fusion center.
Your role is to produce analytical products from provided source material.

Standing instructions:
- All analysis must be grounded in the provided source material
- Clearly distinguish between confirmed facts and analytical assessments
- Use confidence language (likely, possibly, insufficient evidence)
- Flag information gaps and inconsistencies
- Structure all outputs with clear headers and sections
- Never fabricate information or speculate beyond the evidence
- Use professional law enforcement terminology
- All outputs are drafts requiring human review before dissemination
"""
EOF
```

**Step 2: Build and distribute**

```bash
# Build the custom model
ollama create le-analyst -f ~/ollama-modelfiles/Modelfile-analyst

# To share with a colleague, give them the Modelfile
# They run the same commands on their machine
```

**Step 3: Version and iterate**

Name Modelfiles with version numbers as they improve:

```
Modelfile-analyst-v1
Modelfile-analyst-v2
Modelfile-analyst-v3
```

### Sharing Effective Prompts Across Teams

**Recommended approach:**

1. **Start individually** -- each analyst develops prompts for their specific tasks
2. **Test and refine** -- expect 2-4 weeks of iteration before prompts stabilize
3. **Share what works** -- collect effective prompts into a shared directory or knowledge base
4. **Standardize gradually** -- once prompts have been tested by multiple analysts, create Modelfiles
5. **Document what did not work** -- failed prompts are as valuable as successful ones for institutional learning

**Sharing mechanisms:**

| Method | Best For | Limitation |
|--------|----------|------------|
| Shared network folder with Modelfiles | Small teams on the same network | Manual distribution |
| Version-controlled repository | Teams with technical capability | Requires basic Git knowledge |
| Shared document with prompt text | Non-technical teams | No parameter bundling |
| Open WebUI shared prompts | Teams using Open WebUI | Tied to the specific UI |

### Iterating on Prompts

Prompt development is an iterative process. Initial prompts rarely produce optimal results. The refinement cycle typically looks like:

1. **Draft the prompt** based on how the task would be explained to a new analyst
2. **Test with real data** (non-CJI or redacted) and evaluate the output
3. **Identify issues** -- missing sections, wrong format, hallucinated content, wrong tone
4. **Refine the prompt** -- add constraints, clarify instructions, specify format more precisely
5. **Retest** -- run the same data through the refined prompt
6. **Repeat** until output quality meets the standard

**Expect 3-5 iterations** before a prompt is stable for a given task type. After that, the Modelfile approach preserves the refined prompt for consistent reuse.

---

## Common Prompting Mistakes in Law Enforcement Context

### Mistake 1: Being Too Vague

**Bad:**
```
Summarize this.
```

**Why it fails:** The model has no information about desired length, format, audience, or focus areas. The output will be generic and likely not useful.

**Better:**
```
Summarize the following incident report into a 200-word executive summary
for supervisory review. Focus on: what happened, key subjects involved,
evidence collected, and current case status.
```

### Mistake 2: Not Specifying Output Format

**Bad:**
```
Analyze these three incident reports for patterns.
```

**Why it fails:** The model will produce unstructured prose that requires significant reformatting. Output format is one of the highest-leverage improvements in any prompt.

**Better:**
```
Analyze these three incident reports for patterns. Structure your
analysis as follows:

1. TEMPORAL PATTERNS (day/time commonalities)
2. GEOGRAPHIC PATTERNS (location clusters)
3. MO PATTERNS (method similarities)
4. ASSESSMENT (likelihood of linked incidents)
5. RECOMMENDED ACTIONS (next investigative steps)
```

### Mistake 3: Not Including the Review Step

**Bad:**
```
Write a BOLO bulletin for this suspect.
```

**Why it fails:** The output may contain hallucinated physical descriptions, fabricated details, or assumed information that the model added to fill gaps. Without explicit constraints, the model "completes" incomplete information with plausible-sounding fabrications.

**Better:**
```
Write a BOLO bulletin for this suspect using ONLY the following
description. If any standard BOLO field is not covered by the
provided information, mark it as "NOT AVAILABLE" rather than
guessing. Flag any descriptions that are approximate rather
than confirmed.
```

### Mistake 4: Assuming the Model Knows Agency SOPs

**Bad:**
```
Write this report in our standard format.
```

**Why it fails:** The model does not have access to any agency's standard operating procedures, report formats, or internal guidelines. It will produce a generic format that may not match internal requirements.

**Better:**
```
Write this report using the following format:

[PASTE YOUR AGENCY'S REPORT TEMPLATE STRUCTURE HERE]

Use the following field names and section headers exactly as shown.
```

**Best:** Create a Modelfile with the agency's report format embedded in the system prompt, so the format applies automatically to every interaction.

### Mistake 5: Feeding Too Much Context at Once

**Bad:**
```
Here are 47 incident reports from the past month. Identify all patterns.
```

**Why it fails:** 47 incident reports likely exceed the model's effective context window. Even if the model accepts the input, quality degrades significantly as the context grows. See [Model Selection](05-model-selection.md) for context window limitations by model.

**Better:**
```
# Process in batches
# Step 1: Summarize each report individually
# Step 2: Feed summaries (not full reports) into the pattern analysis
# Step 3: Use the pattern analysis prompt on the summary set
```

For long-document workflows, see [Knowledge Management](08-knowledge-management.md) for RAG-based approaches that handle large document sets more effectively.

### Mistake 6: Not Providing Enough Context

**Bad:**
```
What are the crime trends in our area?
```

**Why it fails:** The model does not have access to local crime data, and its training data has a knowledge cutoff. The response will be generic national trends or hallucinated local statistics.

**Better:**
```
Based on the following crime data for [AREA] covering [TIME PERIOD],
identify the three most significant trends:

[PASTE ACTUAL DATA]
```

**Key principle:** The model can only work with what it is given. Always provide the data within the prompt rather than assuming the model has access to agency databases or current local information.

---

## References

- Ollama Modelfile Documentation -- [github.com/ollama/ollama/blob/main/docs/modelfile.md](https://github.com/ollama/ollama/blob/main/docs/modelfile.md)
- Ollama Model Library -- [ollama.com/library](https://ollama.com/library)
- Prompt Engineering Guide -- [promptingguide.ai](https://www.promptingguide.ai/)
- FBI CJIS Security Policy v6.0 -- [FBI CJIS Resource Center](https://www.fbi.gov/services/cjis)

---

*This document is part of the [NFCA Open Source LLM](../README.md) companion resource. For model selection, see the [Model Selection Guide](05-model-selection.md). For ready-to-use prompt templates, see the [`resources/prompt-templates/`](../resources/prompt-templates/) directory. This is an educational resource, not official guidance. Consult your agency's CJIS Systems Officer (CSO) for compliance decisions.*
