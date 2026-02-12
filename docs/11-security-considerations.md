# Security Considerations for AI-Generated Output

> **Summary:** Deploying a local LLM is a technical challenge. Using its output responsibly is a policy challenge. This document addresses the non-technical side of AI deployment: content review standards, disclosure requirements, legal considerations, departmental policy frameworks, incident response for AI misuse, and data handling best practices. These are emerging areas without settled case law or universal standards -- agencies should use this document as a starting point for conversations with their legal counsel, not as a substitute for legal advice.

**Important:** This document flags legal and policy issues for awareness. It does not provide legal advice. Agencies should consult their legal counsel, CJIS Systems Officer, and records management professionals for guidance specific to their jurisdiction and operational context.

---

## Table of Contents

- [AI-Generated Content Review Policy](#ai-generated-content-review-policy)
- [Disclosure Requirements](#disclosure-requirements)
- [Legal Considerations](#legal-considerations)
- [Departmental Policy Framework](#departmental-policy-framework)
- [Incident Response for AI Misuse](#incident-response-for-ai-misuse)
- [Data Handling Best Practices](#data-handling-best-practices)
- [Key Takeaways](#key-takeaways)
- [References](#references)

---

## AI-Generated Content Review Policy

Every piece of AI-generated output must be reviewed by a human before it is used, shared, or disseminated. This is the single most important policy for any agency deploying AI tools.

### The Core Principle

**AI is a drafting tool, not an author.** The human reviewer is always the accountable party. If an AI-generated document contains an error that causes harm, the responsibility lies with the person who reviewed and approved the document -- not with the AI system.

This is not a theoretical concern. LLMs hallucinate. They generate plausible-sounding text that may contain factual errors, misattributions, or fabricated details. See [Understanding LLMs](02-understanding-llms.md) for a detailed explanation of why this occurs. At the 7B-13B parameter scale used in local deployments, hallucination rates are higher than in larger commercial models (see [Limitations and Tradeoffs](10-limitations-tradeoffs.md)).

### Suggested Review Levels by Document Type

Not all documents carry the same risk. A tiered review approach allocates review effort proportionally to the potential consequences of an error.

| Review Level | Description | Applies To | Reviewer Requirements |
|-------------|-------------|-----------|----------------------|
| **Informal** | Quick scan for obvious errors and tone | Internal notes, meeting summaries, training materials, administrative drafts | Any authorized user |
| **Standard** | Line-by-line review of all factual claims, names, dates, and conclusions | Analytical products, intelligence summaries, briefing documents, interagency correspondence | Subject matter expert in the relevant area |
| **Critical** | Dual review by two independent reviewers; all factual claims verified against source material | Products going to prosecutors, court filings, documents supporting warrants, high-visibility intelligence products | Two reviewers: subject matter expert + supervisor |

### Minimum Review Standards

Regardless of review level, every review should verify:

1. **Factual accuracy** -- Are names, dates, locations, case numbers, and other specific details correct?
2. **Source attribution** -- Does the output accurately reflect the source material provided?
3. **Logical coherence** -- Do the conclusions follow from the evidence presented?
4. **Completeness** -- Is anything important from the source material missing or underrepresented?
5. **Fabrication check** -- Does the output contain any details, citations, or claims not present in the source material?
6. **Tone and appropriateness** -- Is the language appropriate for the intended audience and purpose?

---

## Disclosure Requirements

When AI tools contribute to analytical products, transparency with stakeholders is important. Disclosure builds trust and sets appropriate expectations for the consumer of the product.

### When to Disclose AI Assistance

| Scenario | Disclosure Recommended | Rationale |
|----------|----------------------|-----------|
| AI drafted an internal memo later reviewed and edited by an analyst | Optional -- agency preference | Low-stakes internal communication |
| AI assisted in summarizing case files for an intelligence product | Yes | Consumers should know the analytical methodology |
| AI generated portions of a product sent to external partners or prosecutors | Yes | External stakeholders need to assess reliability |
| AI was used to identify patterns or connections in data | Yes | Analytical conclusions should be transparent about methodology |
| AI helped draft training materials using synthetic data | Optional | Low risk; training context makes AI use expected |

### Sample Disclosure Language

Agencies should develop their own disclosure standards. The following examples are generic starting points -- not mandated language:

**Brief notation (for routine products):**
> "This product was drafted with AI assistance and reviewed by [Analyst Name/Title]."

**Detailed notation (for sensitive or externally shared products):**
> "Portions of this analytical product were drafted using a locally deployed AI language model. All content has been reviewed for accuracy by [Analyst Name/Title] and [Supervisor Name/Title]. The AI system processed only information already available to the analyst. Source verification was performed against original source documents."

**Methodology note (for intelligence products):**
> "Analytical methodology: Source documents were processed using an AI language model for initial summarization and pattern identification. All AI-generated findings were independently verified against source material by the authoring analyst. The AI tool operates on local infrastructure with no external data transmission."

### Agency Policy Considerations

Each agency should establish clear policy addressing:

- Whether disclosure is mandatory or discretionary, and for which document types
- Standard disclosure language approved for use
- Where the disclosure notation appears in the document (header, footer, methodology section)
- Whether the disclosure identifies the specific AI tool used or references AI generically
- How disclosure standards apply to products shared with external agencies

---

## Legal Considerations

**The following section identifies emerging legal issues that agencies should be aware of. These are not settled areas of law, and this document does not provide legal advice. Agencies should proactively consult with their legal counsel on each of these topics before deploying AI tools for any workload that may intersect with legal proceedings, records requests, or regulatory compliance.**

### Brady/Giglio Implications

AI-drafted investigative documents may be subject to discovery obligations under *Brady v. Maryland* (1963) and *Giglio v. United States* (1972). Potential issues include:

- **Prompt and response records:** If an analyst uses an AI tool to summarize case evidence and that summary is later used in a prosecution, the underlying AI interaction (prompt and response) may be discoverable as work product
- **AI error disclosure:** If an AI tool produced an erroneous summary that was later corrected, the existence and nature of the error may be subject to disclosure obligations
- **Methodology transparency:** Defense counsel may challenge the reliability of AI-assisted analytical methods and request details about the tools, models, and processes used

**Recommendation:** Agencies should consult their legal counsel regarding whether and how AI interaction logs should be preserved and disclosed under their jurisdiction's discovery rules. Establishing clear records retention for AI interactions before deployment is preferable to retroactive policy development after a discovery dispute arises.

### Evidentiary Considerations

AI-assisted analysis may raise evidentiary questions if the output is introduced or referenced in court proceedings:

- **Admissibility:** Courts may scrutinize the reliability of AI-assisted analytical methods under *Daubert v. Merrell Dow Pharmaceuticals* (1993) or the *Frye* standard, depending on jurisdiction
- **Weight and credibility:** Even if admitted, AI-assisted analysis may be given less weight if the methodology is not well-documented or understood by the trier of fact
- **Expert testimony:** If AI-assisted analysis is challenged, someone must be prepared to explain the tool, its limitations, and the review process applied to its output
- **Chain of custody for AI outputs:** The integrity of AI-generated documents from creation through review to final use should be documentable

**Recommendation:** Agencies should consult their legal counsel and prosecutors' offices about evidentiary standards for AI-assisted work products in their jurisdiction before such products enter the litigation pipeline.

### Records Retention

AI interactions -- including prompts submitted to the model and responses generated -- may be subject to records retention policies:

- **State and local records laws** vary significantly in their treatment of electronic communications, work product, and analytical tools
- **AI interaction logs** may constitute agency records subject to retention schedules, depending on how they are classified under applicable law
- **Prompt content** may contain CJI, PII, or other regulated data subject to its own retention and handling requirements
- **Destruction schedules** should address AI interaction logs explicitly -- both how long to retain them and how to securely dispose of them

**Recommendation:** Agencies should consult their records management professionals and legal counsel to determine whether AI interaction logs constitute records under applicable law and, if so, what retention schedule applies.

### FOIA and Public Records

AI-generated products and, potentially, the underlying AI interactions may be subject to Freedom of Information Act (FOIA) or state public records requests:

- **AI-generated documents** that become part of an agency's files are likely subject to public records laws on the same basis as any other agency document
- **AI interaction logs** (prompts and responses) may or may not be subject to public records requests depending on their classification as records, work product, or deliberative process material
- **Exemptions** may apply (law enforcement investigative records, deliberative process, etc.) but must be evaluated by counsel on a case-by-case basis

**Recommendation:** Agencies should consult their legal counsel regarding the public records status of AI-generated products and interaction logs under their jurisdiction's transparency laws.

### Liability for AI-Assisted Errors

When AI-assisted output contains errors that cause harm, the question of liability is straightforward in principle but complex in application:

- **The human reviewer is responsible, not the AI.** AI systems have no legal personhood and cannot be held liable
- **Organizational liability** may attach if the agency failed to establish adequate review procedures, training, or quality controls for AI-assisted work
- **Individual liability** may attach to the reviewing analyst or supervisor if established review procedures were not followed
- **The standard of care** for AI-assisted work is evolving and not yet well-defined in case law

**Recommendation:** Agencies should consult their legal counsel about liability frameworks for AI-assisted work products, ensure that review policies are documented and followed, and confirm that existing professional liability protections adequately cover AI-assisted analytical processes.

### The Overarching Legal Reality

These are emerging legal questions without settled case law in most jurisdictions. The law enforcement community is in the early stages of integrating AI into operational workflows, and the legal frameworks governing that integration are developing in real time.

Agencies that proactively engage their legal counsel, establish clear policies, and document their processes will be better positioned than those that adopt the technology first and address legal questions reactively.

---

## Departmental Policy Framework

Before deploying AI tools -- even for a non-CJI pilot -- agencies should establish a foundational policy framework. The policy does not need to be exhaustive on day one, but it should address the critical elements below.

### Key Elements of an AI Acceptable Use Policy

The following are recommended sections for an agency AI policy. This is not a complete template -- each agency's policy should be developed with input from legal counsel, IT security, and operational leadership.

| Policy Section | Key Questions to Address |
|---------------|------------------------|
| **Purpose and Scope** | What does this policy cover? Which AI tools? Which personnel? Which workloads? |
| **Authorized Use Cases** | What tasks are approved for AI assistance? Start narrow and expand based on experience |
| **Prohibited Uses** | What is explicitly forbidden? (e.g., processing CJI on unhardened systems, using AI output without review, feeding classified data into unapproved tools) |
| **Data Classification Restrictions** | What data classifications can be processed by the AI system? What classifications are prohibited? Map to your existing data classification scheme |
| **Review Requirements** | What review standard applies to each document type? (Reference the [review levels](#suggested-review-levels-by-document-type) above) |
| **Disclosure Standards** | When and how must AI assistance be disclosed? For which document types and audiences? |
| **Training Requirements** | What training must personnel complete before using AI tools? How is proficiency verified? |
| **Incident Reporting** | How should potential AI misuse, errors, or security incidents be reported? Through what channel and to whom? |
| **Compliance and Enforcement** | What are the consequences of policy violations? How is compliance monitored? |

### Roles and Responsibilities

| Role | Responsibilities |
|------|-----------------|
| **AI System Administrator** | Technical deployment, maintenance, patching, configuration management, security controls |
| **AI Use Policy Owner** | Policy development, updates, exception approvals, compliance oversight |
| **Analyst / End User** | Prompt creation, output review, adherence to use policy, incident reporting |
| **Supervisor / Reviewer** | Review and approval of AI-assisted products per review standards, quality assurance |
| **CJIS Systems Officer** | Compliance validation, ATO authority (if processing CJI), security assessment oversight |
| **Legal Counsel** | Advisory on disclosure, discovery, records retention, liability, and evidentiary matters |
| **Records Manager** | Classification and retention schedule determination for AI interaction logs |

### Training Requirements

All personnel authorized to use AI tools should receive training covering:

1. **How the AI tool works** -- basic understanding of LLMs, including limitations and hallucination (see [Understanding LLMs](02-understanding-llms.md))
2. **Authorized and prohibited uses** -- what the policy allows and forbids
3. **Data classification rules** -- what data can and cannot be processed
4. **Prompting fundamentals** -- how to write effective prompts (see [Prompting Guide](06-prompting-guide.md))
5. **Review procedures** -- how to review AI output for accuracy, completeness, and fabrication
6. **Disclosure requirements** -- when and how to note AI assistance
7. **Incident reporting** -- how to report misuse, errors, or security concerns
8. **Security awareness** -- AI-specific security considerations (prompt injection, data leakage, social engineering via AI)

Training should be documented and repeated annually, consistent with existing CJIS security awareness training cycles.

---

## Incident Response for AI Misuse

AI misuse is a distinct category of security incident that existing incident response plans may not cover. Agencies should establish AI-specific incident definitions, reporting procedures, and remediation steps.

### What Constitutes AI Misuse

| Category | Examples |
|----------|---------|
| **Unauthorized data processing** | Feeding CJI into an unhardened AI system; processing classified data on a system not authorized for that classification |
| **Output misuse** | Disseminating AI-generated output without required human review; presenting AI-generated content as original human analysis without disclosure |
| **Policy violation** | Using AI tools for prohibited purposes (personal use, unauthorized analysis types); bypassing DLP controls |
| **Security incident** | Prompt injection attacks exploiting the AI system; unauthorized access to AI interaction logs; exfiltration of data via AI queries |
| **Accidental exposure** | Inadvertently including CJI in a prompt on a non-CJI-authorized system; sharing AI interaction logs containing sensitive data |

### Reporting Procedures

1. **Immediate containment:** If CJI or other sensitive data was improperly processed, suspend the user's AI access and preserve logs
2. **Report to supervisor:** All suspected AI misuse should be reported through the standard incident reporting chain
3. **Notify IT security:** AI-related security incidents should be reported to the IT security team for technical investigation
4. **CJIS notification:** If CJI was processed on an unauthorized system, consult the CJIS Systems Officer regarding breach notification obligations (1-hour notification requirement under CJIS 5.2)
5. **Document:** Record the incident details, scope of exposure, and containment actions taken

### Remediation Steps

| Step | Action |
|------|--------|
| 1. Contain | Suspend affected user access; isolate the affected system if necessary |
| 2. Assess | Determine what data was exposed, to what system, and the scope of potential impact |
| 3. Preserve | Secure AI interaction logs as evidence; do not delete or modify logs |
| 4. Notify | Follow CJIS breach notification requirements if CJI was involved; notify legal counsel |
| 5. Remediate | Address the root cause (training gap, policy gap, technical control gap) |
| 6. Document | Complete an after-action report documenting the incident, response, and corrective actions |
| 7. Prevent | Implement additional controls to prevent recurrence (enhanced DLP, additional training, policy updates) |

### Prevention Measures

- **Data Loss Prevention (DLP):** Deploy technical controls to prevent CJI from reaching unauthorized systems (see [CJIS Compliance -- DLP section](09-cjis-compliance.md#data-loss-prevention-for-the-non-cji-phase))
- **Training:** Ensure all AI users understand what constitutes misuse before they receive access
- **Access controls:** Limit AI system access to trained, authorized personnel only
- **Monitoring:** Review AI interaction logs regularly for anomalous usage patterns
- **Policy clarity:** Ensure the acceptable use policy is unambiguous about what is and is not permitted

---

## Data Handling Best Practices

Beyond policy, specific data handling practices reduce risk when operating AI tools in a law enforcement environment.

### Prompt Hygiene

- **Never process CJI on unhardened systems.** This is the foundational rule. During a non-CJI pilot, enforce this with [DLP controls](09-cjis-compliance.md#data-loss-prevention-for-the-non-cji-phase), not just policy
- **Sanitize prompts before sharing examples.** When sharing effective prompts with colleagues or in training, remove all real names, case numbers, dates, locations, and identifiers. Use synthetic data in all shared examples
- **Clear model context between sessions.** When switching between topics, cases, or data classifications, start a new conversation or session. Residual context from a previous session should not carry over into an unrelated analysis
- **Minimize data in prompts.** Include only the information necessary for the task. Avoid pasting entire documents when a relevant excerpt would suffice

### Log Management

- **Retain AI interaction logs** according to the retention schedule established by records management and legal counsel
- **Protect log access.** AI interaction logs may contain sensitive data -- limit access to authorized personnel
- **Forward logs to SIEM.** If deployed in a Phase 2 (hardened) configuration, AI interaction logs should be integrated with the existing security information and event management system for correlation and alerting (see [CJIS Compliance -- Audit Logging](09-cjis-compliance.md#gap-3-audit-logging-cjis-57))
- **Tamper protection.** Logs should be stored in a manner that prevents unauthorized modification -- write-once storage, cryptographic integrity verification, or centralized log aggregation

### System Isolation

- **Network segmentation.** AI workstations processing sensitive data should be on a segmented network, consistent with existing security architecture
- **Physical access.** AI workstations should be subject to the same physical security controls as other CJI-processing systems (see [CJIS Compliance -- Physical Security](09-cjis-compliance.md))
- **USB and removable media.** Control removable media access on AI workstations to prevent unauthorized data extraction
- **No personal devices.** AI systems processing agency data should not be accessible from personal devices or networks

### Model Management

- **Use approved models only.** Establish a whitelist of models that have been evaluated and approved for agency use
- **Verify model integrity.** When downloading or transferring model files, validate checksums to ensure files have not been tampered with
- **Document model versions.** Track which model version is deployed, when it was installed, and when it was last updated
- **Do not fine-tune on CJI** without explicit authorization and additional security controls. Fine-tuning embeds training data into model weights, creating a persistent data exposure risk

---

## Key Takeaways

1. **Human review is non-negotiable.** Every AI output must be reviewed before use. The human reviewer is always the accountable party.

2. **Disclosure builds trust.** Transparent acknowledgment of AI assistance in analytical products protects the agency and sets appropriate expectations.

3. **Legal questions are unsettled.** Brady/Giglio, evidentiary standards, records retention, and FOIA implications for AI-assisted work are emerging areas. Engage legal counsel proactively, not reactively.

4. **Policy before technology.** Even a non-CJI pilot should operate under a basic acceptable use policy. Expand the policy as the deployment matures.

5. **AI misuse is a distinct incident category.** Existing incident response plans may not cover AI-specific scenarios. Define what constitutes misuse and how to respond before an incident occurs.

6. **Data handling discipline matters.** Prompt hygiene, log management, system isolation, and model management are the practical controls that prevent policy from being merely aspirational.

7. **Start the legal conversation now.** Agencies that consult their legal counsel before deployment will be better positioned than those that address legal questions after a discovery dispute or records request.

---

## References

- [CJIS Compliance Framework](09-cjis-compliance.md) -- Full compliance mapping, application-layer gaps, and DLP guidance
- [Understanding LLMs](02-understanding-llms.md) -- How LLMs work, hallucination, and temperature settings
- [Limitations and Tradeoffs](10-limitations-tradeoffs.md) -- Capability limitations at 7B-13B scale and honest cloud comparison
- [Prompting Guide](06-prompting-guide.md) -- Prompt engineering for law enforcement workflows
- [Next Steps](12-next-steps.md) -- Implementation roadmap including policy development
- *Brady v. Maryland*, 373 U.S. 83 (1963)
- *Giglio v. United States*, 405 U.S. 150 (1972)
- *Daubert v. Merrell Dow Pharmaceuticals, Inc.*, 509 U.S. 579 (1993)
- FBI CJIS Security Policy v6.0 -- [CJIS Resource Center](https://www.fbi.gov/services/cjis)
- NIST AI Risk Management Framework -- [AI RMF](https://www.nist.gov/artificial-intelligence)
- Executive Order 14110 -- Safe, Secure, and Trustworthy Development and Use of Artificial Intelligence (October 2023)

---

*This document is part of the [Secure and Affordable In-House AI](../README.md) companion resource. It is an educational reference, not legal advice. Agencies should consult their legal counsel, CJIS Systems Officer, and records management professionals for guidance specific to their jurisdiction and operational context.*
