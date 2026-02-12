# Next Steps

> **Summary:** This document provides a practical roadmap for agencies moving from "interested in local LLMs" to "operational deployment." It covers a phased implementation timeline, lessons learned from early adopters, budget request guidance for leadership, a training and onboarding plan for analysts, and a consolidated list of additional resources. The goal is to make the next action clear regardless of where an agency is in its evaluation process.

---

## Table of Contents

- [Implementation Roadmap Summary](#implementation-roadmap-summary)
- [Common Lessons Learned from Early Adopters](#common-lessons-learned-from-early-adopters)
- [Budget Request Guidance](#budget-request-guidance)
- [Training and Onboarding Plan](#training-and-onboarding-plan)
- [Additional Resources](#additional-resources)
- [References](#references)

---

## Implementation Roadmap Summary

Deployment follows four phases with explicit go/no-go gates between them. The critical principle: **CJI is never processed on an unhardened system.** For full technical details on the compliance framework, see [CJIS Compliance](09-cjis-compliance.md).

### Phase 1: Non-CJI Pilot (4 Weeks)

**Objective:** Prove the technology works. Build organizational confidence. Zero CJI risk.

**Cost:** $1,000-$1,500 (hardware) + IT setup time (4-8 hours)

**Actions:**

| Week | Activity |
|------|----------|
| 1 | Install Ollama on a standalone workstation; deploy Open WebUI for a user-friendly interface (see [Ollama Quickstart](03-ollama-quickstart.md)) |
| 1 | Pull 2-3 recommended models for evaluation (see [Model Selection](05-model-selection.md)) |
| 1-2 | Test with non-sensitive administrative tasks: report template generation, training scenario creation, policy drafting, public records summarization |
| 2-3 | Evaluate model quality against actual workflows; document which models perform best for which tasks |
| 3-4 | Collect user feedback from 3-5 pilot users; document time savings, quality impressions, and pain points |
| 4 | Produce a pilot summary report with findings and recommendation |

**What to test during the pilot:**
- Document summarization (provide a public-domain report and ask for an executive summary)
- Template-based generation (BOLOs, situational awareness briefs, training scenarios using synthetic data)
- Research synthesis (open-source intelligence from public records and news sources)
- Administrative drafting (memos, policy sections, meeting summaries)

**What NOT to process during the pilot:**
- Any Criminal Justice Information (CJI)
- Personally Identifiable Information (PII) from real cases
- Classified or sensitive intelligence data
- Any data subject to regulatory restrictions

Deploy [Data Loss Prevention (DLP)](09-cjis-compliance.md#data-loss-prevention-for-the-non-cji-phase) controls to enforce this restriction technically, not just through policy.

**Go/No-Go Gate:** Steering committee reviews pilot results. If the pilot demonstrates measurable value and user acceptance, proceed to Phase 2.

---

### Phase 2: Security Hardening (8-12 Weeks)

**Objective:** Implement all 8 application-layer CJIS controls identified in the [CJIS Compliance Framework](09-cjis-compliance.md#the-8-application-layer-gaps).

**Cost:** Primarily IT staff time; tools referenced are open-source

**Actions:**

| Control | Implementation |
|---------|---------------|
| Authentication (CJIS 5.6) | Deploy reverse proxy with Active Directory integration; enforce MFA |
| Authorization/RBAC (CJIS 5.9) | Implement role-based access control mapping AD security groups to model and data permissions |
| Audit Logging (CJIS 5.7) | Deploy prompt/response logging with SIEM integration; configure 1-year retention |
| Encryption in Transit (CJIS 5.10) | Configure TLS 1.2+ with FIPS 140-2 validated modules for all API communications |
| Encryption at Rest (CJIS 5.10) | Deploy encrypted filesystem volumes for all Ollama data and cache directories |
| Incident Response (CJIS 5.2) | Develop AI-specific SIEM correlation rules and incident response playbook |
| Configuration Management (CJIS 5.13) | Document security configuration baselines for Ollama and all supporting components |
| Media Protection (CJIS 5.11) | Establish cache sanitization procedures and integrate into workstation lifecycle management |

**Go/No-Go Gate:** Security team validates all 8 controls against CJIS requirements before proceeding.

---

### Phase 3: Compliance Validation (4-8 Weeks)

**Objective:** Validate all controls and obtain Authorization to Operate (ATO) for CJI workloads.

**Actions:**

| Activity | Description |
|----------|-------------|
| Internal security assessment | Evaluate all 13 CJIS policy areas against the deployed system |
| Penetration testing | Third-party or internal testing of the hardened deployment |
| System Security Plan (SSP) | Document the complete security posture, controls, and residual risks |
| POA&M | Develop a Plan of Action and Milestones for any residual findings |
| Incident response exercise | Conduct a tabletop exercise using the AI-specific incident response playbook |
| CSO engagement | Present the compliance posture to the CJIS Systems Officer for ATO recommendation |
| ATO decision | Leadership signs the ATO based on CSO recommendation |

**Go/No-Go Gate:** ATO must be signed before any CJI data is processed on the system. No exceptions.

---

### Phase 4: CJI Production (Ongoing)

**Objective:** Begin processing CJI workloads with full controls in place.

**Actions:**

| Activity | Description |
|----------|-------------|
| Controlled rollout | Start with 2-3 approved CJI use cases and a limited user group |
| Monitoring | Active monitoring of all security controls, audit logs, and usage patterns from day one |
| Monthly reviews | Security review meetings for the first 6 months to identify issues early |
| Expanded rollout | Gradually add use cases and users based on monitoring results and user feedback |
| Ongoing compliance | Maintain patching cadence, log review, incident response readiness, and annual security reassessment |

### Phase Timeline

```
Phase Timeline (Approximate)

Month:  1    2    3    4    5    6    7    8+
        |----|----|----|----|----|----|----|---->

Phase 1:|####|
        Non-CJI Pilot (zero CJI risk)
             |-- GO/NO-GO --|
Phase 2:     |################|
             Security Hardening
                              |-- GO/NO-GO --|
Phase 3:                      |##########|
                              Compliance Validation
                                         |-- GO/NO-GO
Phase 4:                                  |########|--->
                                          CJI Production (ongoing)
```

**Note:** These timelines are approximate. Actual duration depends on agency size, IT capacity, existing security infrastructure, and CSO engagement timelines.

---

## Common Lessons Learned from Early Adopters

The following observations reflect common patterns reported by organizations deploying local LLMs in structured environments. They are practical insights, not guarantees of specific outcomes.

### Hardware Matters -- Do Not Compromise on VRAM

The single most impactful hardware decision is GPU VRAM. Attempting to run models on insufficient VRAM results in dramatically slower performance (CPU offloading) or inability to run useful models at all. The recommended minimum for practical use is 16GB VRAM. See the [Hardware Guide](04-hardware-guide.md) for specifications and cost analysis.

Organizations that purchased underpowered hardware to save money often ended up replacing it within months -- effectively paying twice.

### Prompt Refinement Takes Time -- Budget for It

New users consistently underestimate the learning curve for effective prompting. The first week of use typically produces mixed results, leading to premature conclusions that "the AI doesn't work." By weeks 3-4, with practice and feedback, most analysts develop prompting skills that produce consistently useful output.

**Practical recommendation:** Set a minimum evaluation period of 4 weeks before making adoption decisions. Judging the technology based on day-one results is unreliable. See the [Prompting Guide](06-prompting-guide.md) for frameworks that accelerate this learning.

### Start with Non-CJI Administrative Tasks

The most successful deployments begin with low-stakes, non-CJI tasks that provide immediate, visible value without compliance complexity:

- Report template generation
- Training scenario creation with synthetic data
- Policy and procedure drafting
- Meeting summary generation
- Public records and open-source intelligence synthesis

These use cases build user confidence and organizational familiarity before introducing the compliance overhead of CJI workloads.

### Draft Generation Quality Often Exceeds Expectations

A common pattern: initial skepticism gives way to genuine utility once analysts develop effective prompts. The first-draft quality of AI-generated summaries, briefs, and templates is typically better than expected -- not perfect, but a substantial improvement over starting from a blank page.

The 80/20 observation holds consistently: AI produces a roughly 80% complete first draft; the analyst applies judgment and domain expertise for the remaining 20%. See [Limitations and Tradeoffs](10-limitations-tradeoffs.md) for a detailed discussion of where this works and where it does not.

### Model Selection Matters -- Test Multiple Models

Different models perform differently on different tasks. An agency's best model for document summarization may not be its best model for report drafting. Testing 2-3 models on actual representative tasks (not benchmarks) during the pilot phase saves time and improves long-term satisfaction. See [Model Selection](05-model-selection.md) for task-model matching guidance.

### Management Buy-In Requires Demonstrated Value

Theoretical arguments for AI adoption rarely convince leadership. A working pilot that demonstrates measurable time savings on real (non-CJI) workflows is significantly more persuasive than a slide deck about AI potential.

**Practical recommendation:** During Phase 1, quantify specific outcomes: "This summary that previously took 2 hours was produced in 15 minutes of AI-assisted drafting and review." Concrete metrics drive budget decisions.

---

## Budget Request Guidance

### Key Line Items for Procurement Requests

| Line Item | Estimated Cost | Notes |
|-----------|---------------|-------|
| GPU workstation (RTX 4060 Ti 16GB or equivalent) | $1,000-$1,500 | One-time capital expenditure; see [Hardware Guide](04-hardware-guide.md) for specifications |
| Monitor, peripherals (if not repurposing existing) | $200-$400 | Optional -- use existing equipment if available |
| IT setup and configuration | 4-8 hours staff time | May be absorbed into existing IT workload |
| Ongoing electricity | $15-$30/month | Under regular use; negligible during idle periods |
| Ongoing IT maintenance | 2-4 hours/month | Patching, troubleshooting, model updates |
| Analyst training time | 8-16 hours per analyst | Initial onboarding; absorbed into professional development |

**Total Year 1 cost:** Approximately $1,500-$2,500 (hardware + setup + first year of electricity and maintenance)

**Total Year 2+ cost:** Approximately $200-$600/year (electricity + maintenance time only)

### How to Frame the Value Proposition for Leadership

The strongest argument for local LLMs is specific to the agency's situation. The following framing approaches are commonly effective:

**Data sovereignty framing:**
> "This gives us AI capability for workloads where no cloud tool is authorized. The alternative is not 'use ChatGPT' -- it is 'continue doing this work manually.'"

**Cost efficiency framing:**
> "One workstation at $1,500 replaces $3,000-$7,000 per year in cloud AI subscriptions for a 10-person team, with the added benefit that no data leaves our infrastructure."

**Analyst productivity framing:**
> "Our pilot showed that AI-assisted drafting reduced the time for [specific task] from [X hours] to [Y minutes], freeing analyst hours for higher-value analytical work."

**Risk management framing:**
> "A non-CJI pilot carries zero compliance risk and lets us evaluate the technology before committing to security hardening. If it doesn't work, the only cost is a workstation that can be repurposed."

### When Leadership Asks "Why Not Microsoft Copilot?"

This is a common and reasonable question. The comparison should be factual and acknowledge that both approaches have merit.

| Factor | Local LLM | Microsoft Copilot / Azure AI |
|--------|-----------|------------------------------|
| **Data location** | All data stays on local hardware | Data processed in Microsoft cloud (Azure Government for CJIS) |
| **Recurring cost** | Electricity only (~$200-$400/year) | $30-$60+/user/month ($3,600-$7,200/year for 10 users) |
| **CJIS compliance** | Requires 8 application-layer controls (see [CJIS Compliance](09-cjis-compliance.md)) | Requires CJIS Security Addendum with Microsoft; compliance path exists but is not automatic |
| **Internet requirement** | None after initial setup | Required for every query |
| **Model capability** | 7B-13B range; good for drafting, limited for complex reasoning | GPT-4 class; stronger reasoning and broader knowledge |
| **Vendor dependency** | None -- switch tools at any time | Locked into Microsoft ecosystem and pricing |
| **Timeline to deploy** | Days to weeks for non-CJI pilot | Depends on procurement, licensing, and CJIS addendum timeline |

**Key message:** These are not mutually exclusive options. An agency can deploy a local LLM for immediate, low-cost, non-CJI capability while evaluating commercial options for longer-term CJI-authorized use. Skills developed locally (prompting, workflow integration, policy) transfer directly to commercial tools.

### Sample Justification Language

The following is generic language adaptable to procurement requests. Agencies should modify it to reflect their specific context and requirements.

> **Purpose:** Procurement of a dedicated workstation for evaluation of locally deployed AI language models for analytical workflow support.
>
> **Justification:** Current agency analytical workflows require significant manual effort for document summarization, report drafting, pattern identification, and intelligence product development. Commercially available AI tools (ChatGPT, Microsoft Copilot, Google Gemini) are prohibited for workloads involving Criminal Justice Information due to data sovereignty requirements. A locally deployed AI system processes all data on agency-owned hardware with no external data transmission, addressing the data sovereignty requirement while providing AI-assisted drafting capability.
>
> **Scope:** Phase 1 evaluation limited to non-CJI administrative and analytical tasks. No Criminal Justice Information will be processed during the evaluation period. Data Loss Prevention controls will be deployed to enforce this restriction technically.
>
> **Cost:** Approximately $1,500 one-time hardware cost. No recurring licensing fees. Ongoing cost limited to electricity and IT maintenance staff time.
>
> **Risk:** Minimal. Phase 1 carries zero compliance risk (no CJI processing). Hardware can be repurposed if the evaluation does not demonstrate sufficient value.
>
> **Expected Outcome:** Evaluation report within 4 weeks documenting time savings, output quality, user satisfaction, and recommendation for continuation or termination.

---

## Training and Onboarding Plan

### Getting the First Analyst Productive (Suggested 1-Week Onboarding)

| Day | Activity | Resources |
|-----|----------|-----------|
| 1 | Overview of AI tools: what LLMs are, what they can and cannot do, hallucination reality | [Understanding LLMs](02-understanding-llms.md), [Limitations](10-limitations-tradeoffs.md) |
| 1 | Hands-on: first prompts using Open WebUI with a simple task (summarize a public news article) | [Ollama Quickstart](03-ollama-quickstart.md) |
| 2 | Prompt engineering fundamentals: role, context, task, format, constraints | [Prompting Guide](06-prompting-guide.md) |
| 2 | Practice: draft a template-based product using provided prompt templates | [Prompt Templates](../resources/prompt-templates/) |
| 3 | Advanced prompting: iterative refinement, chain-of-thought, handling poor output | [Prompting Guide](06-prompting-guide.md) |
| 3 | Practice: work through 2-3 use cases from the use case library with synthetic data | [Use Cases](07-use-cases.md) |
| 4 | Policy and security: acceptable use policy review, data classification rules, review standards, disclosure requirements | [Security Considerations](11-security-considerations.md) |
| 4 | Practice: complete a simulated workflow end-to-end (draft, review, disclose) | -- |
| 5 | Independent work: analyst applies AI tools to a real non-CJI task from their current workload | -- |
| 5 | Debrief: discuss results, questions, and areas for further practice | -- |

### Building Internal Champions

The most effective training model for AI tools in organizational settings is peer-based: one or two analysts learn the tool deeply and then train others.

**Champion selection criteria:**
- Analytical role with regular drafting and summarization tasks
- Willingness to experiment and iterate (comfort with imperfect first results)
- Credibility with peers (recommendations carry weight)
- Availability to spend time during the pilot learning the tool

**Champion responsibilities:**
- Complete the 1-week onboarding and an additional week of independent exploration
- Develop agency-specific prompt templates for common workflows
- Serve as the first point of contact for peer questions
- Provide feedback to IT on system performance and usability
- Present pilot findings to leadership

### Sharing Prompts and Best Practices

Effective prompts are organizational assets. Establish a mechanism for sharing and refining them:

- **Shared prompt library:** A folder (on the local network, not cloud) containing tested prompt templates for common tasks
- **Version control:** Track which prompts work best for which tasks and which models; retire prompts that are superseded
- **Regular sharing sessions:** Brief (15-30 minute) team meetings to share effective prompts, discuss challenges, and distribute updates
- **Documentation standard:** Each shared prompt should include the task it addresses, which model it works best with, any known limitations, and the last date it was tested

### Ongoing Skill Development

AI tools and models improve continuously. Training should not be a one-time event.

- **Model updates:** When new models are deployed, brief users on any changes in behavior or capability
- **Prompt refinement:** As analysts gain experience, revisit and improve shared prompt templates
- **New use cases:** As the deployment matures, identify new workflows that could benefit from AI assistance
- **Community resources:** Encourage analysts to follow developments in AI tools relevant to their work (Ollama release notes, Open WebUI updates, model comparison benchmarks)

---

## Additional Resources

### Tools Referenced in This Guide

| Tool | URL | Description |
|------|-----|-------------|
| Ollama | [https://ollama.com](https://ollama.com) | Open-source local LLM runtime |
| Open WebUI | [https://github.com/open-webui/open-webui](https://github.com/open-webui/open-webui) | ChatGPT-like web interface for Ollama |
| Microsoft Presidio | [https://github.com/microsoft/presidio](https://github.com/microsoft/presidio) | Open-source Data Loss Prevention engine |
| LiteLLM | [https://github.com/BerriAI/litellm](https://github.com/BerriAI/litellm) | API proxy with authentication, RBAC, and logging |

### Government and Compliance Resources

| Resource | URL | Description |
|----------|-----|-------------|
| FBI CJIS Security Policy | [https://www.fbi.gov/services/cjis](https://www.fbi.gov/services/cjis) | CJIS Security Policy Resource Center |
| Executive Order 14110 | [https://www.whitehouse.gov/briefing-room/presidential-actions/2023/10/30/executive-order-on-the-safe-secure-and-trustworthy-development-and-use-of-artificial-intelligence/](https://www.whitehouse.gov/briefing-room/presidential-actions/2023/10/30/executive-order-on-the-safe-secure-and-trustworthy-development-and-use-of-artificial-intelligence/) | Safe, Secure, and Trustworthy AI (October 2023) |
| FedRAMP | [https://www.fedramp.gov](https://www.fedramp.gov) | Federal Risk and Authorization Management Program |
| NIST AI RMF | [https://www.nist.gov/artificial-intelligence](https://www.nist.gov/artificial-intelligence) | AI Risk Management Framework |
| FedRAMP Marketplace | [https://marketplace.fedramp.gov](https://marketplace.fedramp.gov) | Searchable catalog of authorized cloud services |

### Community and Learning

| Resource | Description |
|----------|-------------|
| Ollama GitHub Issues | Community support, bug reports, and feature requests |
| Ollama Discord | Community discussion and troubleshooting |
| Open WebUI Documentation | Setup guides, configuration options, and feature documentation |
| r/LocalLLaMA (Reddit) | Active community discussing local LLM deployment, model comparisons, and hardware |

### Related Documents in This Guide

| Document | When to Read |
|----------|-------------|
| [Why Local LLMs](01-why-local-llms.md) | Understanding the value proposition and when local deployment makes sense |
| [Understanding LLMs](02-understanding-llms.md) | How LLMs work, hallucination, and temperature settings |
| [Ollama Quickstart](03-ollama-quickstart.md) | Getting Ollama installed and running in 30 minutes |
| [Hardware Guide](04-hardware-guide.md) | Detailed hardware specifications and cost analysis |
| [Model Selection](05-model-selection.md) | Which models for which tasks with exact pull commands |
| [Prompting Guide](06-prompting-guide.md) | Prompt engineering for law enforcement workflows |
| [Use Cases](07-use-cases.md) | Real-world use cases with example prompts |
| [Knowledge Management](08-knowledge-management.md) | RAG architecture and connecting local data to LLMs |
| [CJIS Compliance](09-cjis-compliance.md) | Full compliance framework with 13-area mapping and 8 application-layer gaps |
| [Limitations and Tradeoffs](10-limitations-tradeoffs.md) | Honest limitations and cloud comparison |
| [Security Considerations](11-security-considerations.md) | Legal, policy, and review standards for AI-generated output |

---

## References

- [CJIS Compliance Framework](09-cjis-compliance.md) -- Phased compliance approach and 8 application-layer gaps
- [Limitations and Tradeoffs](10-limitations-tradeoffs.md) -- Capability boundaries and honest cloud comparison
- [Security Considerations](11-security-considerations.md) -- Legal, policy, and review standards for AI output
- [Hardware Guide](04-hardware-guide.md) -- Detailed cost analysis and hardware specifications
- [Model Selection](05-model-selection.md) -- Task-model matching and exact pull commands
- [Prompting Guide](06-prompting-guide.md) -- Prompt engineering frameworks
- FBI CJIS Security Policy v6.0 -- [CJIS Resource Center](https://www.fbi.gov/services/cjis)
- Executive Order 14110 -- Safe, Secure, and Trustworthy Development and Use of Artificial Intelligence (October 2023)
- FedRAMP -- [fedramp.gov](https://www.fedramp.gov/)
- NIST AI Risk Management Framework -- [nist.gov/artificial-intelligence](https://www.nist.gov/artificial-intelligence)

---

*This document is part of the [Secure and Affordable In-House AI](../README.md) companion resource. It is an educational reference, not official guidance. Consult your agency's CJIS Systems Officer (CSO) for compliance decisions.*
