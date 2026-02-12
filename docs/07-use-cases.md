# Real-World Use Cases for Local LLMs in Law Enforcement

> **Summary:** Local LLMs are useful when applied to the right tasks with realistic expectations. This document provides five concrete use cases with copy-paste-ready prompt templates, honest assessments of what works and what does not, and answers to the questions that practitioners will ask. Every use case follows the same principle: the LLM produces a first draft, and a human applies judgment to the final product.

---

## Table of Contents

- [How to Use This Document](#how-to-use-this-document)
- [Use Case 1: Monday Morning Situational Awareness Brief](#use-case-1-monday-morning-situational-awareness-brief)
- [Use Case 2: Case File Summarization](#use-case-2-case-file-summarization)
- [Use Case 3: Training Scenario Generation](#use-case-3-training-scenario-generation)
- [Use Case 4: Policy and Procedure Drafting](#use-case-4-policy-and-procedure-drafting)
- [Use Case 5: Open Source Intelligence Synthesis](#use-case-5-open-source-intelligence-synthesis)
- [Use Case Summary Table](#use-case-summary-table)
- [Common Questions and Honest Answers](#common-questions-and-honest-answers)
- [References](#references)

---

## How to Use This Document

Each use case follows a consistent structure:

| Section | Purpose |
|---------|---------|
| **Scenario** | When and why this task arises |
| **CJI Classification** | Whether this use case involves Criminal Justice Information |
| **Prompt Template** | Copy-paste-ready prompt in a code block |
| **Expected Output** | What the model should produce |
| **Honest Assessment** | What works, what does not, and where human judgment is required |
| **Tips** | Practical guidance for better results |

All prompt templates assume Ollama with a model in the 7B-13B parameter range (e.g., Llama 3.1 8B, Mistral 7B, Qwen3-8B). For guidance on prompt structure and engineering principles, see the [Prompting Guide](06-prompting-guide.md). For model selection by task type, see the [Model Selection Guide](05-model-selection.md).

**The 80/20 Rule:** Across all use cases, the LLM typically produces a first draft that is roughly 80% usable. The remaining 20% requires human judgment -- verifying facts, adjusting tone, adding context the model could not know, and ensuring accuracy. The value is not in eliminating the human step. The value is in compressing hours of initial drafting into minutes.

---

## Use Case 1: Monday Morning Situational Awareness Brief

### Scenario

A typical scenario involves multiple incident reports accumulating over a weekend -- assaults, burglaries, traffic incidents, warrant services, and other activity. By Monday morning, command staff need a consolidated situational awareness brief summarizing what happened, identifying patterns, and highlighting items requiring follow-up. An analyst traditionally spends 1.5 to 2 hours reading individual reports, extracting key details, and drafting the brief manually.

### CJI Classification

**Depends on data source.** If the brief references incident report numbers, subject names, victim information, or other CJI, this use case requires a CJIS-hardened deployment (Phase 4). If the brief uses only public-facing information (press releases, public call logs), it can be used during the non-CJI pilot phase. See the [CJIS Compliance Framework](09-cjis-compliance.md) for phasing guidance.

### Prompt Template

```
You are a law enforcement intelligence analyst preparing a Monday morning
situational awareness brief for command staff. Your role is to synthesize
multiple incident reports into a concise, actionable briefing document.

CONTEXT:
The following are incident summaries from the past 72 hours (Friday evening
through Sunday). Read each incident and extract the key details.

[PASTE INCIDENT SUMMARIES HERE]

TASK:
Create a situational awareness brief with the following structure:

1. EXECUTIVE SUMMARY (3-5 sentences)
   - Total number of significant incidents
   - Most critical items requiring immediate command attention
   - Any emerging patterns or trends

2. INCIDENT SUMMARY TABLE
   For each incident, provide:
   - Date/Time
   - Location (general area, not exact address)
   - Incident Type
   - Brief Description (1-2 sentences)
   - Status (resolved, under investigation, pending follow-up)
   - Priority Level (routine, elevated, critical)

3. PATTERN ANALYSIS
   - Geographic clustering (are incidents concentrated in specific areas?)
   - Temporal patterns (time-of-day or day-of-week trends)
   - Modus operandi similarities across incidents
   - Any connections between incidents worth investigating

4. RECOMMENDED FOLLOW-UP
   - Items requiring command decision or resource allocation
   - Investigations needing additional support
   - Community impact considerations
   - Suggested patrol emphasis areas based on patterns

5. INFORMATION GAPS
   - What is missing from the reports that would improve analysis
   - Pending lab results, witness interviews, or surveillance review

CONSTRAINTS:
- Use clear, professional language appropriate for a command staff briefing
- Do not speculate beyond what the incident reports support
- Flag any items where information is incomplete or ambiguous
- If you identify a pattern, explain your reasoning
- Do not fabricate details not present in the source reports
```

### Expected Output

The model should produce a structured document following the five sections above. At the 7B-13B parameter level, expect:

- **Executive summary:** Generally accurate and well-structured
- **Incident table:** Reliable extraction of facts from provided text
- **Pattern analysis:** Basic geographic and temporal pattern identification; may miss subtle connections
- **Follow-up recommendations:** Reasonable but generic; the analyst will need to add agency-specific operational context
- **Information gaps:** Often identifies obvious gaps; may miss less apparent ones

### Honest Assessment

In testing, this type of task reduced a typical 2-hour process to roughly 15 minutes of review and editing. The model handles the mechanical work -- reading each report, extracting structured data, and organizing it into a consistent format -- effectively. The analyst's time shifts from data extraction to quality review and judgment.

**What works well:**
- Extracting structured facts from unstructured narrative reports
- Organizing information into a consistent briefing format
- Identifying straightforward geographic and temporal patterns

**What requires human judgment:**
- Validating that extracted facts match the source reports (spot-check, do not assume accuracy)
- Adding operational context the model cannot know (staffing, ongoing operations, community dynamics)
- Evaluating whether identified "patterns" are meaningful or coincidental
- Adjusting priority assessments based on institutional knowledge

### Tips

- **More context produces better output.** Pasting full incident narratives yields better results than brief summaries. The model works with what it receives.
- **Run the prompt twice.** If the first output misses something, run it again. Non-deterministic outputs mean the second pass may catch what the first missed. Compare both.
- **Build a standing prompt.** Save the prompt template in a Modelfile (see the [Prompting Guide](06-prompting-guide.md)) and reuse it every Monday. Consistency in prompting produces consistency in output.

---

## Use Case 2: Case File Summarization

### Scenario

A case file has grown to 200 or more pages over the course of an investigation -- witness statements, forensic reports, surveillance logs, supplemental reports, and correspondence. An executive summary is needed for a supervisor, a prosecutor, or a multi-agency briefing. Manual summarization of a file this size can take half a day or longer.

### CJI Classification

**Almost certainly CJI.** Case files contain subject names, victim information, witness statements, and investigative details. This use case requires a CJIS-hardened deployment (Phase 4). See the [CJIS Compliance Framework](09-cjis-compliance.md).

### The Context Window Challenge

At the 7B-13B parameter level, most models have a context window of 4,096 to 8,192 tokens (roughly 3,000 to 6,000 words). A 200-page case file exceeds this by an order of magnitude. The file cannot simply be pasted into a single prompt.

**The workaround: chunk, summarize, then synthesize.**

| Step | Action | Input | Output |
|------|--------|-------|--------|
| 1 | Divide the case file into logical sections | Full case file | 8-12 section chunks |
| 2 | Summarize each section individually | One section per prompt | One summary per section |
| 3 | Combine all section summaries into a single prompt | All section summaries | Final executive summary |

### Prompt Template -- Step 2: Section Summary

```
You are a law enforcement analyst summarizing a section of a case file.
Extract only the facts present in the text. Do not add interpretation
or speculation.

CASE FILE SECTION: [Section name, e.g., "Witness Statements"]

[PASTE SECTION TEXT HERE]

TASK:
Provide a structured summary of this section including:

1. KEY FACTS
   - List each distinct factual claim with its source
     (e.g., "Witness A stated..." or "Forensic report concluded...")
   - Include dates, times, locations, and involved parties

2. EVIDENCE ITEMS
   - Physical evidence referenced
   - Digital evidence referenced
   - Documentary evidence referenced

3. CONTRADICTIONS OR INCONSISTENCIES
   - Note any conflicting statements between sources
   - Flag any timeline discrepancies

4. GAPS
   - What questions does this section raise but not answer?
   - What follow-up would strengthen this section?

CONSTRAINTS:
- Attribute every claim to its source document or witness
- Use "according to [source]" language — never state facts as independently verified
- If information is ambiguous, say so explicitly
- Do not summarize information that is not present in this section
```

### Prompt Template -- Step 3: Synthesis

```
You are a law enforcement analyst preparing an executive summary from
multiple section summaries of a case file.

SECTION SUMMARIES:

[PASTE ALL SECTION SUMMARIES FROM STEP 2 HERE]

TASK:
Synthesize these section summaries into a single executive summary with
the following structure:

1. CASE OVERVIEW (1 paragraph)
   - What is this case about, in plain language
   - Key subjects, victims, and timeframe

2. INVESTIGATION TIMELINE
   - Chronological sequence of key events and investigative actions
   - Use a numbered list with dates

3. KEY EVIDENCE
   - Most significant evidence items across all sections
   - Strength assessment (strong, moderate, needs corroboration)

4. WITNESS SUMMARY
   - Brief characterization of each witness's contribution
   - Note any corroboration or contradiction between witnesses

5. CURRENT STATUS
   - Where the investigation stands
   - Outstanding leads and pending actions

6. ASSESSMENT
   - Strength of the case based on available evidence
   - Critical gaps that must be addressed
   - Recommended next steps

CONSTRAINTS:
- This summary must be self-contained — a reader should understand the
  case without reading the full file
- Attribute key claims to their sources
- Do not introduce information not present in the section summaries
- Flag any areas where section summaries contradict each other
```

### Honest Assessment

The chunk-and-synthesize approach works, but it introduces limitations:

**What works well:**
- Extracting structured facts from narrative text within each section
- Producing consistent formatting across sections
- Generating a readable executive summary from multiple inputs

**What does not work well:**
- Cross-referencing details across sections (e.g., noting that Witness A's statement contradicts the forensic timeline) -- the model may miss these when processing sections independently
- Maintaining context between chunks -- details from Section 1 are not available when processing Section 8
- Subtle inferences that require reading the entire file as a whole

**Mitigation:** The synthesis step (Step 3) partially addresses cross-referencing, but it works from summaries, not original text. An analyst should specifically review the synthesis output for cross-section connections the model may have missed.

**Time savings estimate:** A 200-page case file that takes 4-6 hours to summarize manually can typically be processed in 45-60 minutes using this approach (including chunking time and human review). The analyst's role shifts from reading every page to reviewing and refining the synthesized output.

### Tips

- **Chunk by logical sections, not by page count.** Witness statements together, forensic reports together, surveillance logs together. This produces more coherent section summaries.
- **Keep section summaries.** Do not discard the Step 2 outputs. They serve as an intermediate reference and can be re-synthesized if the final summary needs revision.
- **For longer context windows:** Models like Llama 3.1 support up to 128K tokens in theory, but performance degrades significantly on commodity hardware at those lengths. Chunking remains the practical approach for 7B-13B models on standard hardware. See the [Hardware Guide](04-hardware-guide.md) for context window and VRAM considerations.

---

## Use Case 3: Training Scenario Generation

### Scenario

A training unit needs realistic but fictional scenarios for tabletop exercises, field training, report-writing practice, or academy instruction. Creating detailed, realistic scenarios from scratch is time-consuming. An LLM can generate synthetic scenarios that feel authentic without using any real case data.

### CJI Classification

**Non-CJI by design.** This use case generates purely synthetic data. No real names, case numbers, addresses, or investigative details are used as input or expected as output. This is a strong candidate for the non-CJI pilot phase (Phase 1).

**Critical safety note:** Real CJI data must never be used as input for training scenario generation, even on a hardened system. The output could inadvertently reproduce identifiable details from real cases, creating disclosure risks. Always use fully synthetic inputs or general parameters (e.g., "a residential burglary series in a suburban jurisdiction" rather than details from an actual case).

### Prompt Template

```
You are a law enforcement training developer creating realistic but
entirely fictional training scenarios. All names, locations, dates,
and details must be completely fabricated. Do not reference any real
people, places, or events.

SCENARIO PARAMETERS:
- Scenario Type: [e.g., residential burglary series, domestic violence
  response, active threat, traffic stop escalation, fraud investigation]
- Complexity Level: [basic, intermediate, advanced]
- Target Audience: [academy recruits, field training officers, detectives,
  command staff tabletop]
- Training Objective: [e.g., report writing practice, decision-making under
  stress, multi-agency coordination, evidence preservation]
- Duration: [e.g., 30-minute exercise, 2-hour tabletop, full-day simulation]

TASK:
Generate a complete training scenario including:

1. SCENARIO OVERVIEW
   - Setting (fictional jurisdiction, date, time, location)
   - Initial dispatch information (what the responding unit is told)
   - Background context (what has led to this point)

2. SCENARIO TIMELINE
   - Sequence of events as they unfold
   - Decision points where trainees must choose a course of action
   - Inject points (new information introduced at specific times)

3. ROLE CARDS
   - For each role in the scenario (responding officer, supervisor,
     witness, subject, dispatcher), provide:
     - Character background (1-2 sentences)
     - What this character knows
     - How this character is likely to behave
     - Key dialogue or statements

4. FACILITATOR GUIDE
   - Expected decision points and correct/acceptable responses
   - Common mistakes to watch for
   - Discussion questions for the debrief
   - Learning objectives mapped to each decision point

5. EVALUATION CRITERIA
   - Observable behaviors to assess
   - Scoring rubric or checklist for each training objective

CONSTRAINTS:
- ALL names, locations, and details must be entirely fictional
- Do not use names or details that could be confused with real cases
- Scenarios should reflect realistic operational conditions
- Include diverse community demographics and situational complexity
  appropriate to the complexity level
- Do not include content that trivializes violence or victimization
```

### Expected Output

The model typically generates a detailed, multi-page scenario with realistic operational flavor. At the 7B-13B parameter level:

- **Scenario overview and timeline:** Generally well-structured and operationally plausible
- **Role cards:** Adequate but sometimes generic; may need agency-specific terminology adjustments
- **Facilitator guide:** Reasonable decision points; discussion questions tend to be high quality
- **Evaluation criteria:** Functional but may need calibration to specific training standards

### Honest Assessment

Training scenario generation is one of the strongest use cases for local LLMs because it involves zero CJI risk and plays to the model's strengths (creative structured generation from parameters). The output serves as a detailed first draft that a training unit can customize.

**What works well:**
- Generating realistic operational details and timelines
- Creating multiple interlocking role cards with consistent internal logic
- Producing discussion questions and learning objectives

**What requires human judgment:**
- Ensuring scenarios reflect current policy and legal standards
- Calibrating complexity to the actual skill level of the audience
- Adding agency-specific procedures, radio codes, and terminology
- Reviewing for any content that could be insensitive or inappropriate

### Tips

- **Generate multiple scenarios and pick the best.** Run the prompt 3-4 times with the same parameters. Each output will differ. Select the strongest foundation and refine it.
- **Layer complexity.** Start with a "basic" scenario, then run a follow-up prompt asking the model to add complications (e.g., "Add a media arrival at the 15-minute mark" or "Add a language barrier with the primary witness").
- **Build a scenario library.** Over time, save the best generated scenarios. They become reusable training assets.

---

## Use Case 4: Policy and Procedure Drafting

### Scenario

Administrative and operational policies require periodic updates -- use-of-force policies aligned to new legislation, social media policies, technology acceptable use policies, records retention schedules, and similar documents. Policy drafting involves research, cross-referencing regulatory requirements, and producing structured documents that follow organizational formatting standards. This is mechanical but time-intensive work.

### CJI Classification

**Non-CJI.** Policy documents are administrative in nature. The inputs (regulatory text, model policies from professional organizations, existing policy templates) are public or internal administrative documents, not criminal justice information. This is a safe non-CJI pilot use case (Phase 1).

### Prompt Template

```
You are a policy analyst assisting with drafting an organizational policy
document. The output should be professional, clear, and structured for
a law enforcement agency context.

POLICY PARAMETERS:
- Policy Topic: [e.g., Artificial Intelligence Acceptable Use Policy]
- Regulatory Basis: [e.g., state law reference, accreditation standard,
  federal guidance — paste relevant text below if available]
- Existing Policy: [paste current policy text here if updating, or
  "New policy — no existing version" if creating from scratch]
- Reference Material: [paste any model policies, best practices, or
  regulatory language the policy should align with]
- Target Audience: [all sworn personnel, civilian staff, specific unit]

TASK:
Draft a policy document with the following structure:

1. POLICY STATEMENT
   - Purpose of the policy (2-3 sentences)
   - Scope (who and what this policy applies to)
   - Effective date placeholder

2. DEFINITIONS
   - Key terms used in the policy with clear definitions
   - Terms should be specific enough to avoid ambiguity

3. POLICY PROVISIONS
   - Numbered sections covering each substantive requirement
   - Each provision should be one clear, enforceable statement
   - Reference applicable regulatory requirements where relevant

4. ROLES AND RESPONSIBILITIES
   - Who is responsible for compliance
   - Who is responsible for enforcement
   - Who is responsible for periodic review

5. PROCEDURES
   - Step-by-step procedures for implementing key provisions
   - Approval workflows where applicable
   - Reporting requirements

6. TRAINING REQUIREMENTS
   - Initial training for affected personnel
   - Recurring training schedule
   - Documentation of training completion

7. COMPLIANCE AND ACCOUNTABILITY
   - How compliance will be monitored
   - Consequences for non-compliance
   - Appeal or grievance process

8. REVIEW SCHEDULE
   - Frequency of policy review (typically annual)
   - Trigger events requiring immediate review
   - Responsible party for initiating review

CONSTRAINTS:
- Use clear, unambiguous language — avoid jargon where possible
- Each provision should be independently understandable
- Do not include provisions that contradict the referenced regulatory basis
- Flag any areas where the regulatory basis is ambiguous or silent
- Use "shall" for mandatory requirements, "should" for recommendations,
  and "may" for discretionary provisions
```

### Expected Output

The model produces a structured policy document that follows standard policy formatting conventions. The output typically runs 3-6 pages depending on policy complexity.

### Honest Assessment

Policy drafting is a strong LLM use case because the task is fundamentally structural -- organizing requirements into a standard format with clear language. The model excels at this.

**What works well:**
- Producing well-organized policy structures with consistent formatting
- Generating clear definitions and procedural steps
- Identifying common provisions that most policies of a given type should include

**What requires human judgment:**
- Ensuring alignment with specific state and local laws (the model's training data may not reflect the most current legislation)
- Adding agency-specific language, chain of command references, and form numbers
- Validating that provisions are actually enforceable under the agency's authority
- Legal review before adoption -- AI-drafted policy language should receive the same legal review as any draft policy

**Time savings estimate:** A policy document that typically takes 6-10 hours of research and drafting can be reduced to 2-3 hours of review, revision, and customization.

### Tips

- **Provide the regulatory basis text directly.** Do not rely on the model's knowledge of specific laws or standards. Paste the relevant regulatory text into the prompt. The model works from what it is given, not from what it "knows."
- **Iterate in sections.** If the overall structure is good but one section needs work, run a follow-up prompt focused only on that section with additional context.
- **Compare against model policies.** Organizations like IACP, PERF, and Lexipol publish model policies. Use the LLM draft as a starting point, then cross-reference against professional model policies for completeness.

---

## Use Case 5: Open Source Intelligence Synthesis

### Scenario

An analyst needs to synthesize information from multiple open sources -- public court records, news articles, business filings, social media (public posts), property records, and other publicly available data -- into an intelligence product. The sources are individually accessible but require synthesis across multiple documents to produce a coherent analytical picture.

### CJI Classification

**Non-CJI by definition.** Open source intelligence (OSINT) is derived from publicly available sources. No CJI is involved in the inputs or outputs. This is an ideal non-CJI pilot use case (Phase 1).

**Note:** If OSINT analysis is later combined with CJI data in a fused intelligence product, the combined product becomes CJI and requires appropriate handling. The OSINT synthesis step itself, however, is non-CJI.

### Prompt Template

```
You are an intelligence analyst synthesizing open source information
into a structured analytical product. All source material is from
publicly available records and media. Your analysis should be objective,
source-attributed, and clearly distinguish between established facts
and analytical assessments.

SOURCE MATERIAL:

Source 1: [Type, e.g., "News article, City Herald, January 15, 2025"]
[Paste text or key excerpts]

Source 2: [Type, e.g., "Public court record, Case No. 2025-CV-1234"]
[Paste text or key excerpts]

Source 3: [Type, e.g., "Business filing, Secretary of State database"]
[Paste text or key excerpts]

[Add additional sources as needed]

ANALYTICAL FOCUS:
[Describe what the analyst is trying to understand, e.g., "Identify
connections between the listed business entities and assess whether
the pattern of business registrations suggests coordinated activity."]

TASK:
Produce an intelligence summary with the following structure:

1. EXECUTIVE SUMMARY (3-5 sentences)
   - Key findings in plain language
   - Confidence level (high, moderate, low) with brief justification
   - Recommended action or further research direction

2. SOURCE EVALUATION
   - For each source, assess:
     - Reliability (established outlet, official record, unverified, etc.)
     - Relevance to the analytical focus
     - Currency (how recent is the information)
   - Note any sources that contradict each other

3. KEY FINDINGS
   - Numbered list of distinct analytical findings
   - Each finding must cite at least one source
   - Distinguish between facts ("Source 2 states...") and assessments
     ("Based on Sources 1 and 3, it appears likely that...")

4. LINK ANALYSIS
   - Connections between persons, organizations, locations, or events
     identified across sources
   - Strength of each connection (confirmed, probable, possible)
   - Gaps where a connection is suspected but not supported by
     available sources

5. TIMELINE
   - Chronological reconstruction of key events from all sources
   - Note any timeline gaps or inconsistencies

6. INFORMATION GAPS AND RECOMMENDED COLLECTION
   - What additional open source information would strengthen the analysis
   - Specific databases, records, or sources to check
   - Questions that remain unanswered

7. ANALYTICAL CONFIDENCE ASSESSMENT
   - Overall confidence in the key findings
   - Factors that increase confidence (corroboration, official records)
   - Factors that decrease confidence (single source, dated information,
     unverified claims)

CONSTRAINTS:
- Every factual claim must be attributed to a specific source
- Clearly label assessments and inferences as such — never present
  analytical judgment as established fact
- Use the Intelligence Community's standard confidence language
  (low, moderate, high confidence) with justification
- Do not introduce information not present in the provided sources
- If sources conflict, present both versions and note the discrepancy
```

### Expected Output

The model produces a structured intelligence summary that follows standard analytical tradecraft formatting. The output quality depends heavily on the quality and quantity of source material provided.

### Honest Assessment

OSINT synthesis is a natural fit for LLMs because the task involves organizing, cross-referencing, and structuring information from multiple text sources -- exactly what language models do well.

**What works well:**
- Extracting and organizing facts from multiple narrative sources
- Identifying explicit connections between entities mentioned across sources
- Producing structured, readable analytical products
- Generating source-attributed findings

**What requires human judgment:**
- Evaluating source reliability beyond surface-level assessment
- Identifying connections that require domain expertise or contextual knowledge
- Assessing the significance of findings in operational context
- Determining whether the analytical confidence assessment is actually calibrated correctly
- Ensuring the product meets dissemination standards for the intended audience

**Time savings estimate:** An OSINT synthesis product that takes 2-4 hours to compile manually can typically be drafted in 20-30 minutes with this approach, followed by 30-45 minutes of analyst review and refinement.

### Tips

- **Provide full source text, not summaries.** The model extracts more detail from full text than from analyst-summarized excerpts.
- **Be specific about the analytical focus.** A vague focus ("tell me about this person") produces vague analysis. A specific focus ("assess the relationship between Entity A and Entity B based on business filings and news coverage") produces targeted output.
- **Use the link analysis section as a starting point, not a conclusion.** The model identifies connections it can see in the text. It cannot identify connections that require specialized knowledge, database access, or inference beyond the provided sources.

---

## Use Case Summary Table

| Use Case | CJI Status | Deployment Phase | Time Savings | Model Strength |
|----------|-----------|-----------------|--------------|----------------|
| Situational Awareness Brief | Depends on data | Phase 1 or 4 | ~2 hours to ~15 min review | High -- structured extraction |
| Case File Summarization | Almost certainly CJI | Phase 4 | ~4-6 hours to ~45-60 min | Moderate -- chunking required |
| Training Scenario Generation | Non-CJI | Phase 1 | 4-8 hours to ~1 hour | High -- creative generation |
| Policy/Procedure Drafting | Non-CJI | Phase 1 | 6-10 hours to ~2-3 hours | High -- structural tasks |
| OSINT Synthesis | Non-CJI | Phase 1 | 2-4 hours to ~1 hour | High -- multi-source synthesis |

For deployment phasing details, see the [CJIS Compliance Framework](09-cjis-compliance.md).

---

## Common Questions and Honest Answers

### "Can the output from this tool be used in official reports that go to the DA's office?"

This is a policy question, not a technology question, and the answer depends entirely on the agency's internal policy. The technology produces text. Whether that text can be submitted in an official capacity depends on:

- Whether the agency has an AI acceptable use policy that addresses AI-assisted drafting
- Whether the reviewing authority (supervisor, prosecutor) is aware that AI assisted in the drafting
- Whether the agency has established a disclosure standard (e.g., "AI-assisted analysis" notation)
- Whether the output has been reviewed and validated by a qualified human before submission

**Recommendation:** Before using AI-assisted output in any official capacity, the agency should establish a written policy addressing disclosure requirements, review standards, and accountability for AI-assisted products. See [Security Considerations](11-security-considerations.md) for a discussion of legal and policy implications, including Brady/discovery considerations.

### "What if the LLM gets something wrong in the brief?"

It will. Not every time, and not catastrophically in most cases, but errors will occur. This is why every use case in this document includes a mandatory human review step. The appropriate mental model is:

- The LLM is a **drafting assistant**, not an author
- The human reviewer is **always accountable** for the final product
- Review means **verifying claims against source material**, not skimming for formatting

Common error types at the 7B-13B parameter level:

| Error Type | Frequency | Impact | Mitigation |
|-----------|-----------|--------|------------|
| Factual extraction errors | Occasional | High | Spot-check key facts against source reports |
| Misattribution (wrong source for a fact) | Occasional | Moderate | Verify source citations |
| Hallucinated details not in source | Rare with good prompts | High | Check for claims without clear source |
| Missed information (omission) | Common | Moderate | Cross-reference output against source list |
| Formatting or structural issues | Common | Low | Easy to fix in review |

For more on model limitations, see [Understanding LLMs](02-understanding-llms.md) and [Limitations and Tradeoffs](10-limitations-tradeoffs.md).

### "Does this work with our Records Management System or CAD system?"

Probably not directly. Ollama does not have native integrations with RMS or CAD platforms. The practical workflow today is:

1. **Export or copy** relevant data from the RMS/CAD system
2. **Paste** the data into the LLM prompt (or upload via Open WebUI if configured)
3. **Review** the LLM output
4. **Copy** the finished product back into the appropriate system

This is a copy-paste workflow, not an automated integration. It adds a manual step but preserves complete human control over what enters and exits the LLM.

Future integration options exist (API connections, middleware, RAG pipelines) but represent Phase 2+ considerations. For more on connecting data sources to the LLM, see [Knowledge Management](08-knowledge-management.md).

### "How accurate is the output?"

Accuracy varies by task type, model, prompt quality, and the clarity of the source material. General observations from testing:

| Task Type | Typical Accuracy | Notes |
|-----------|-----------------|-------|
| Factual extraction from provided text | 85-95% | Improves with clear source formatting |
| Summarization | 80-90% | May miss nuance; verify key points |
| Pattern identification | 70-85% | Finds obvious patterns; misses subtle ones |
| Creative generation (scenarios, drafts) | N/A | Accuracy is less relevant; quality and usability matter |
| Cross-reference analysis | 65-80% | Better with fewer sources; degrades with complexity |

These are approximate ranges based on general testing with 7B-13B parameter models. Actual performance will vary by model, prompt design, and task complexity. Any deployment should include an initial testing period where output is systematically validated against manual work to establish a local accuracy baseline.

### "Is there a way to avoid the copy-paste step?"

For the initial deployment, copy-paste is the practical workflow. However, several tools can reduce the friction:

- **Open WebUI** supports document upload and can process files directly (see [Knowledge Management](08-knowledge-management.md))
- **Modelfiles** can embed system prompts so the role definition and output format do not need to be pasted every time (see [Prompting Guide](06-prompting-guide.md))
- **Saved prompt templates** in Open WebUI allow one-click loading of common workflows

Full automation (direct RMS/CAD integration, automatic report ingestion) is technically possible but requires additional development and carries additional compliance considerations. This is a roadmap item, not a day-one capability.

---

## References

- [Ollama](https://ollama.com) -- Local LLM runtime
- [Open WebUI](https://github.com/open-webui/open-webui) -- Chat interface for Ollama
- [Ollama Modelfile Documentation](https://github.com/ollama/ollama/blob/main/docs/modelfile.md) -- System prompt configuration
- FBI CJIS Security Policy v6.0 -- [CJIS Resource Center](https://www.fbi.gov/services/cjis)

---

*This document is part of the [Secure and Affordable In-House AI](../README.md) companion resource. It is an educational resource, not official guidance. Consult your agency's CJIS Systems Officer (CSO) for compliance decisions.*
