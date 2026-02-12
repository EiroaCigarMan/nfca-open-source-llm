# Limitations and Tradeoffs

> **Summary:** Local LLMs are a powerful tool for law enforcement, but they are not a universal solution. This document provides an honest assessment of capability limits at the 7B-13B parameter scale, operational realities that affect day-to-day use, situations where cloud alternatives may be the better choice, and straightforward answers to common skeptic questions. An informed decision requires understanding both the strengths and the boundaries.

---

## Table of Contents

- [Capability Limitations at 7B-13B Scale](#capability-limitations-at-7b-13b-scale)
- [Operational Limitations](#operational-limitations)
- [When NOT to Use Local LLMs](#when-not-to-use-local-llms)
- [The Honest Cloud Comparison](#the-honest-cloud-comparison)
- [What to Tell Skeptics](#what-to-tell-skeptics)
- [The Bottom Line](#the-bottom-line)
- [References](#references)

---

## Capability Limitations at 7B-13B Scale

Local models running on commodity hardware ($1,000-$1,500 workstations with 16GB VRAM) operate at the 7B-13B parameter range. These models are genuinely useful, but they are meaningfully less capable than large commercial models (GPT-4, Claude, Gemini) that run on massive cloud infrastructure. Understanding the specific boundaries prevents frustration and misuse.

### Where Small Models Perform Well

| Task | Quality Level | Notes |
|------|--------------|-------|
| Document summarization | Good | Extracting key points from provided text is a strength |
| First-draft generation | Good | Reports, memos, briefs, BOLOs -- produces usable drafts requiring human editing |
| Template-based generation | Good | Structured outputs following a defined format |
| Pattern identification (with context) | Moderate | Can identify patterns when relevant data is provided in the prompt |
| Information extraction | Good | Pulling specific data points from unstructured text |
| Text classification | Good | Categorizing documents, triaging information |

### Where Small Models Struggle

| Task | Quality Level | Why |
|------|--------------|-----|
| Complex multi-step reasoning | Poor | Chains of logic beyond 3-4 steps degrade significantly at 7B-13B scale |
| Nuanced legal analysis | Poor | Legal reasoning requires understanding of precedent, statutory interpretation, and contextual judgment that small models lack |
| Long document comprehension | Limited | Context windows at this scale typically handle 4K-8K tokens effectively; a 200-page case file exceeds this without [RAG architecture](08-knowledge-management.md) |
| Mathematical calculations | Unreliable | Even simple arithmetic can produce incorrect results; never rely on LLM-generated calculations without verification |
| Broad world knowledge | Limited | Smaller models have less parametric knowledge than 70B+ models; they "know" less about the world |
| Multi-language tasks | Variable | Quality drops for non-English languages, though some models (Qwen) handle multilingual tasks better than others |
| Code generation | Moderate | Functional for simple scripts; unreliable for complex logic |

### The 80/20 Expectation

For most law enforcement analytical workflows, a local 7B-8B model handles approximately 80% of the drafting and processing work. The remaining 20% requires human judgment -- verifying facts, correcting inaccuracies, adding analytical context, and making the final determination.

This is the correct way to frame the value proposition: **the model produces a first draft; the analyst produces the final product.** The time savings come from starting with a structured draft rather than a blank page, not from eliminating human review.

For detailed model recommendations and task-capability mapping, see the [Model Selection Guide](05-model-selection.md).

---

## Operational Limitations

Beyond model capability, several operational realities affect day-to-day use of local LLMs.

### Learning Curve

Effective prompting is a skill. New users should expect a 2-4 week learning curve before becoming proficient at writing prompts that consistently produce useful output. Common early frustrations include:

- Prompts that are too vague, producing generic responses
- Not providing enough context for the model to work with
- Expecting the model to "know" agency-specific procedures or terminology
- Frustration when the model hallucinates -- which it will

Organizations should budget for this ramp-up time and avoid judging the technology based on day-one results. The [Prompting Guide](06-prompting-guide.md) provides frameworks to accelerate this learning.

### Model Updates and Maintenance

Unlike cloud services that update automatically, local models require manual management:

- **Model updates** must be downloaded, tested, and deployed deliberately
- **Ollama updates** (the runtime itself) must be applied manually, especially for security patches
- **Air-gapped systems** require a defined transfer process -- downloading updates on a connected system, transferring via approved media, and installing on the isolated system (see [CJIS Compliance](09-cjis-compliance.md) for patching procedures)
- **No automatic vulnerability patching** -- someone must monitor for CVEs and apply fixes

Designate a responsible party for monitoring updates and maintaining a patching cadence. Without this, systems drift out of date and accumulate unpatched vulnerabilities.

### Collaboration Limitations

A default local LLM deployment is a single-workstation tool:

- **No built-in multi-user access** -- Ollama serves one machine by default
- **No shared conversation history** across users without additional tooling
- **No centralized prompt library** without deliberate configuration
- **No real-time collaboration** -- analysts cannot co-edit or share sessions natively

Multi-user access is possible through tools like [Open WebUI](https://github.com/open-webui/open-webui) with network configuration, but this adds complexity and security considerations. For teams requiring real-time collaboration across locations, cloud solutions may be more practical.

### Single Point of Failure

A single workstation running Ollama has no built-in redundancy:

- Hardware failure eliminates AI capability entirely
- No automatic failover to a backup system
- Recovery time depends on having spare hardware and backup configurations
- Mission-critical workflows should not depend solely on a local LLM without a contingency plan

### Community Support Model

Local LLMs operate on a community support model, not a vendor support agreement:

- **No SLA** for response time to issues
- **No guaranteed fix timeline** for bugs or vulnerabilities
- **Support channels** are GitHub issues, Discord communities, and forums
- **No phone number to call** when something breaks at 2 AM

This is a significant consideration for agencies accustomed to vendor support contracts. The trade-off is clear: community support is free but comes with no guarantees; vendor support costs money but provides accountability.

---

## When NOT to Use Local LLMs

Local deployment is not always the right answer. The following scenarios favor alternative approaches.

### When Commercial-Grade Reasoning Is Required

Tasks requiring sophisticated multi-step reasoning, nuanced policy interpretation, or complex analytical synthesis are better served by larger commercial models. If the analytical task is so critical that a 7B-8B model's reasoning limitations create unacceptable risk, a FedRAMP-authorized cloud AI service with appropriate data handling agreements may be more appropriate.

### When Budget Exists and Compliance Is Simple

For agencies that already operate within Azure Government or AWS GovCloud with CJIS Security Addenda in place, adding a cloud-based AI service to the existing compliance envelope may be simpler and less expensive than building and maintaining a parallel local infrastructure. The compliance engineering required for local LLM CJI workloads (see [CJIS Compliance](09-cjis-compliance.md)) is non-trivial.

### When a Single User Does Non-Sensitive Work

For one analyst processing only non-sensitive, publicly available information, a cloud AI subscription at $20-$30/month is likely cheaper than a dedicated $1,000-$1,500 workstation. The ROI for local deployment strengthens with multiple users and sensitive data requirements.

### When IT Support for Maintenance Is Unavailable

Local LLM deployment requires ongoing technical maintenance: software updates, security patching, troubleshooting, and configuration management. If no one within the organization has the technical capability and time allocation to maintain the system, it will degrade. An unmaintained system is worse than no system -- it creates a false sense of capability while accumulating security debt.

### When Real-Time Cross-Location Collaboration Is Required

If the primary use case requires analysts in multiple physical locations to collaborate in real time on AI-assisted analysis, cloud-based solutions are architecturally better suited. Local deployment can support remote access through network configuration, but this adds complexity and may conflict with security requirements.

---

## The Honest Cloud Comparison

This is not "local is always better." Both approaches have legitimate strengths, and the right choice depends on specific agency requirements.

| Factor | Local LLM (Ollama) | Azure OpenAI (FedRAMP High) | ChatGPT Team / Enterprise |
|--------|--------------------|-----------------------------|---------------------------|
| **Data sovereignty** | Complete -- data never leaves the machine | Data stays within Azure Government boundary; covered by CJIS Security Addendum | Data processed on commercial cloud; not suitable for CJI |
| **CJIS compliance path** | Requires 8 application-layer controls (see [CJIS Compliance](09-cjis-compliance.md)) | Pre-built compliance framework; still requires agency-level assessment | Not CJIS compliant; no CJIS Security Addendum available |
| **Model capability** | 7B-13B parameter range; good for drafting, limited for complex reasoning | GPT-4 class; significantly stronger reasoning, broader knowledge | GPT-4 class; strongest commercial capability |
| **Cost structure** | One-time hardware ($1,000-$1,500) + electricity + IT time | Per-token or per-user pricing; ongoing operational cost | $25-$60/user/month; scales linearly with headcount |
| **Cost at 1 user** | Higher (hardware amortization) | Moderate | Lower ($300-$720/year) |
| **Cost at 10 users** | Lower (no per-user licensing) | Higher ($3,000-$10,000+/year) | Higher ($3,000-$7,200/year) |
| **Maintenance burden** | Agency-managed: updates, patching, troubleshooting | Vendor-managed: automatic updates, SLA-backed support | Vendor-managed: zero maintenance |
| **Internet dependency** | None after initial setup | Required for every query | Required for every query |
| **Scaling** | Hardware-limited; new users may require additional workstations | Instant scaling; add users through portal | Instant scaling; add seats through portal |
| **Availability** | Depends on local hardware uptime | 99.9%+ SLA from Microsoft | 99.9%+ SLA from OpenAI |
| **Vendor lock-in** | None -- switch models or tools at any time | Moderate -- API-specific; migration requires engineering | High -- proprietary interface and features |
| **Model control** | Full -- choose models, versions, configurations | Limited -- use what Microsoft offers | None -- use what OpenAI provides |

### Key Decision Points

1. **CJI workloads with limited budget:** Local LLM is the strongest option. Cloud CJI-compliant services are expensive and still require compliance validation.
2. **Non-CJI workloads, single user:** Cloud subscription is simpler and cheaper.
3. **Multi-user teams with sensitive data:** Local LLM provides the best long-term ROI.
4. **Agencies already in Azure Government:** Adding Azure OpenAI to an existing compliance envelope may be the path of least resistance.
5. **Disconnected or field environments:** Local LLM is the only option.

---

## What to Tell Skeptics

Honest answers to common pushback questions encountered when proposing local LLM deployment.

### "I tried ChatGPT and it made things up. Why would this be different?"

**Honest answer:** It will also make things up sometimes. All LLMs hallucinate -- they generate plausible-sounding text that may contain factual errors. The difference is not that local models are more accurate (they are generally less accurate than GPT-4 for complex tasks). The difference is:

1. **Data sovereignty** -- sensitive information never leaves the local machine, which is not true for ChatGPT
2. **Human-in-the-loop** -- all output is treated as a draft requiring human review, not as a final product
3. **Appropriate use cases** -- local models are deployed for specific tasks where they perform well (summarization, drafting, extraction), not as general-purpose oracles

The framing matters: this is a drafting assistant, not an infallible analyst. See [Understanding LLMs](02-understanding-llms.md) for a deeper explanation of why hallucination occurs and how to manage it.

### "Is this just a fad?"

**Honest answer:** The underlying technology is not a fad. Large language models represent a fundamental shift in how text processing, summarization, and generation tasks are performed. The trajectory over the past several years has been consistent improvement in both capability and efficiency.

That said, specific tools and platforms will change. Ollama may be superseded by a better runtime. Model architectures will evolve. The investment here is in capability (AI-assisted analytical workflows) and infrastructure (hardware, security controls, organizational policy), not in any single vendor or product. The hardware purchased for a local LLM today will run whatever model is released next year.

### "Who supports this if something breaks?"

**Honest answer:** Community support -- GitHub issues, Discord, forums. There is no vendor support SLA, no 24/7 helpdesk, and no contractual obligation to fix anything on any timeline. This is a genuine limitation.

Mitigations include:
- Maintaining internal documentation of the deployment configuration
- Ensuring at least two staff members understand the system architecture
- Keeping the non-CJI pilot phase low-stakes so that a system failure is inconvenient, not mission-critical
- Evaluating commercial support options (some companies offer enterprise support for open-source AI stacks) if the pilot succeeds and the agency wants to scale

### "Why not just wait for Microsoft Copilot to get CJIS certified?"

**Honest answer:** Waiting is a valid strategy. Microsoft is actively pursuing CJIS compliance for its AI products through the Azure Government pathway. However:

1. **Timeline is uncertain** -- there is no published date for full CJIS certification of Copilot-class features within Azure Government
2. **Cost will be significant** -- enterprise AI licensing in government tiers is expensive, typically $30-$60+ per user per month
3. **Data still leaves your infrastructure** -- even with CJIS Security Addendum, data is processed in Microsoft's data centers, not on local hardware
4. **Opportunity cost** -- waiting means no AI capability during the waiting period; a non-CJI local pilot can start immediately with zero compliance risk

These approaches are not mutually exclusive. An agency can run a local LLM pilot today while monitoring the commercial compliance landscape. Skills developed with local models (prompting, workflow integration, policy development) transfer directly to commercial tools if the agency later adopts one.

### "Our analysts aren't technical enough for this"

**Honest answer:** Most analysts can become proficient with a local LLM within 2-4 weeks, provided the deployment includes a user-friendly interface (not command-line only) and basic training.

The key factors:
- **Open WebUI** provides a ChatGPT-like interface that requires no technical skills to use (see [Ollama Quickstart](03-ollama-quickstart.md))
- **Prompt templates** reduce the learning curve by giving analysts starting points for common tasks (see [Prompting Guide](06-prompting-guide.md))
- **The initial barrier is prompting skill, not technical skill** -- writing good prompts is an analytical skill, not a technical one
- **Internal champions** who learn the tool first and then train others are more effective than formal training programs

If the concern is about the IT team's ability to deploy and maintain the infrastructure, that is a separate and valid concern addressed in [Operational Limitations](#operational-limitations) above.

---

## The Bottom Line

Local LLMs at the 7B-13B scale are **useful but limited tools**. They excel at structured drafting, summarization, extraction, and template-based generation. They struggle with complex reasoning, nuanced analysis, and tasks requiring broad knowledge.

The honest pitch is not "this replaces cloud AI" or "this is just as good as GPT-4." The honest pitch is:

- **For sensitive data workloads:** Local LLMs provide AI capability where no cloud tool can be used at all. The alternative is not "use ChatGPT instead" -- it is "do this work manually."
- **For multi-user teams:** The cost structure favors local deployment over per-seat cloud licensing.
- **For operational independence:** No internet dependency means AI capability in any environment.

The limitations are real but manageable. The tradeoffs are honest and well-defined. The decision should be based on specific agency requirements, not on enthusiasm or skepticism alone.

---

## References

- [Model Selection Guide](05-model-selection.md) -- Task-capability mapping for specific models
- [Hardware Guide](04-hardware-guide.md) -- Detailed cost analysis and hardware specifications
- [CJIS Compliance Framework](09-cjis-compliance.md) -- Full 13-area compliance mapping and 8 application-layer gaps
- [Understanding LLMs](02-understanding-llms.md) -- How LLMs work, hallucination, and temperature settings
- [Prompting Guide](06-prompting-guide.md) -- Prompt engineering for law enforcement workflows
- [Ollama Quickstart](03-ollama-quickstart.md) -- Getting started with Ollama and Open WebUI
- [Security Considerations](11-security-considerations.md) -- Legal and policy considerations for AI-generated output
- [Next Steps](12-next-steps.md) -- Implementation roadmap and budget guidance
- [Azure OpenAI Service](https://azure.microsoft.com/en-us/products/ai-services/openai-service) -- Microsoft's FedRAMP-authorized AI offering
- [FedRAMP Marketplace](https://marketplace.fedramp.gov/) -- Searchable catalog of authorized cloud services

---

*This document is part of the [Secure and Affordable In-House AI](../README.md) companion resource. It is an educational reference, not official guidance. Consult your agency's CJIS Systems Officer (CSO) for compliance decisions.*
