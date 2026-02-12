# Why Local LLMs Matter for Law Enforcement

> **Summary:** AI tools are banned for Criminal Justice Information workloads — and that ban is correct. But the analytical need hasn't gone away. Local LLM deployment lets you bring AI capability inside your security boundary, where the data never leaves your control. This document makes the case honestly: where local deployment wins, where it doesn't, and what you need to consider before committing.

---

## Table of Contents

- [The Capability Gap](#the-capability-gap)
- [Three Value Drivers for Local Deployment](#three-value-drivers-for-local-deployment)
- [Honest Caveats](#honest-caveats)
- [When Local Deployment Makes Sense — and When It Doesn't](#when-local-deployment-makes-sense--and-when-it-doesnt)
- [The Bottom Line](#the-bottom-line)
- [Next Steps](#next-steps)

---

## The Capability Gap

Most law enforcement agencies prohibit external AI tools — ChatGPT, Claude, Gemini, Microsoft Copilot — for any workload involving Criminal Justice Information (CJI). This prohibition is sound policy. CJI data, including Social Security numbers, arrest records, and investigative case files, must never transit to servers outside government control.

**But the ban eliminates the tool without eliminating the workload.**

The underlying analytical needs persist:

- **Document analysis** — Case file summarization, evidence review across hundreds of pages, report drafting, and cross-reference identification. A 200-page case file that takes an analyst 3 hours to manually summarize can be processed by an LLM in minutes.
- **Pattern recognition** — Timeline reconstruction from multiple source reports, link analysis between subjects, locations, and events, and trend identification across case databases.
- **Research and drafting** — Open-source intelligence synthesis, regulatory compliance research, training material development, and administrative document preparation.

These tasks consume significant analyst hours every week — hours spent on mechanical processing rather than the analytical judgment that humans do best.

### The Competitive Landscape

This isn't a theoretical problem. Peer agencies are deploying AI capabilities:

- **Executive Order 14110** (October 2023) directed federal agencies to accelerate responsible AI adoption
- The **FedRAMP AI authorization pathway** continues expanding the catalog of approved AI services
- State and local agencies deploying AI tools — whether cloud-based or self-hosted — report measurable improvements in case processing speed, pattern identification accuracy, and report quality

Agencies without AI capability face a growing analytical throughput gap. The question is no longer *whether* to adopt AI, but *how* to do it within your security requirements.

### Why External Cloud APIs Are Problematic for CJI

The prohibition on external AI tools is grounded in specific, legitimate concerns:

| Concern | Detail |
|---------|--------|
| **CJI data privacy** | Prompts containing SSNs, case numbers, arrest records, and witness information must not transit to external servers |
| **Approval complexity** | Even FedRAMP High authorized services require organizational approval processes, security addenda, and ongoing compliance monitoring |
| **Availability risk** | Cloud dependency means an internet outage eliminates AI capability entirely |
| **Cost unpredictability** | Per-query pricing models create budget uncertainty as usage scales |
| **Vendor lock-in** | Proprietary models and APIs create dependency on a single vendor's pricing decisions and product roadmap |

> **Important nuance:** Cloud alternatives — specifically Azure OpenAI Service with FedRAMP High authorization and Microsoft CJIS Security Addendum — represent a viable path for organizations whose risk tolerance and operational requirements differ. Local deployment is not "always better." It depends on your specific requirements. See [Limitations and Tradeoffs](10-limitations-tradeoffs.md) for an honest comparison.

---

## Three Value Drivers for Local Deployment

Three developments have converged to make self-hosted AI practical for law enforcement:

### 1. Data Sovereignty

**All processing occurs within your infrastructure.** When you run a local LLM, your prompts and responses never leave the machine. There is no external API call, no cloud processing, no data transmission to review in your security posture.

This is the strongest argument for local deployment. You maintain complete control over:

- What data enters the system
- Where processing occurs
- Where outputs are stored
- Who has access to interaction logs

For agencies handling CJI, sensitive intelligence products, or classified information, this level of control matters. No vendor assurance, terms of service, or data processing agreement provides the same guarantee as "the data physically cannot leave this machine."

### 2. Cost Predictability

**Fixed hardware investment versus recurring subscriptions.** A capable local AI workstation costs $1,000–$1,500 in hardware. After that, the per-query cost is electricity — typically a few cents per day under normal use.

Compare this to cloud AI pricing models:

- Commercial AI subscriptions charge $20–$30 per user per month
- Enterprise/government tiers often cost significantly more
- Usage-based API pricing creates unpredictable monthly costs
- Costs scale linearly with users and usage

The cost advantage of local deployment grows with:
- **Number of users** — One workstation can serve a small team
- **Volume of queries** — No per-query cost ceiling
- **Duration of use** — Hardware depreciates but doesn't bill monthly

> **Caveat:** The total cost of local deployment is higher than hardware alone. See [Honest Caveats](#honest-caveats) below for a complete picture. For detailed cost analysis, see the [Hardware Guide](04-hardware-guide.md).

### 3. Operational Independence

**No internet dependency, no vendor outages, no service changes.** A local LLM runs on your hardware with your power supply. It works during:

- Internet outages
- Cloud provider incidents
- Vendor pricing changes or service discontinuations
- Network maintenance windows

For agencies that need AI capability in environments with limited or no connectivity — field operations, secure facilities, mobile command posts — local deployment is the only option.

Additionally, you control the model versions, update schedule, and configuration. No vendor can push an update that changes model behavior, modifies terms of service, or deprecates a capability you depend on.

---

## Honest Caveats

The presentation case for local LLMs is strong, but intellectual honesty requires acknowledging the limitations. An informed decision is better than an oversold one.

### Air-Gap Alone Does Not Ensure Compliance

This is the most important caveat for law enforcement professionals. "The data stays on my machine" addresses **one** aspect of compliance — data transmission. But CJIS Security Policy v6.0 has **13 policy areas**, including:

- Access control and authentication
- Audit logging and accountability
- Encryption (in transit AND at rest)
- Incident response
- Configuration management
- Personnel security

A default Ollama installation satisfies roughly 5 of 13 CJIS policy areas. The remaining 8 require deliberate application-layer hardening. Air-gap is necessary but not sufficient.

> **For full details:** See the [CJIS Compliance Framework](09-cjis-compliance.md), which maps all 13 policy areas to Ollama's default capabilities and identifies the 8 application-layer gaps with remediation paths.

### Initial Model Download Requires Internet

True air-gap operation is a **post-setup** capability. You need internet connectivity to:

- Download and install Ollama
- Pull model files (typically 4–8 GB per model)
- Download any additional tools (Open WebUI, etc.)

After initial setup, the system can operate fully disconnected. For environments that are already air-gapped, models must be transferred via approved media (USB, cross-domain solution) from a connected system.

### Total Cost Exceeds Hardware Alone

The $1,000–$1,500 hardware figure is accurate for the machine itself. But total cost of ownership includes:

| Cost Category | Estimate |
|---------------|----------|
| Hardware | $1,000–$1,500 (one-time) |
| Electricity | $15–$30/month under regular use |
| IT setup time | 4–8 hours for initial deployment |
| Ongoing IT support | Periodic maintenance, troubleshooting |
| Model updates | Manual effort, especially on air-gapped systems |
| Training | Analyst onboarding and prompt development time |

For a detailed cost breakdown and comparison with cloud alternatives, see the [Hardware Guide](04-hardware-guide.md).

### Capability Gap vs. Commercial Models

Local models running on commodity hardware (7B–13B parameters) are **less capable** than large commercial models (GPT-4, Claude). They work well for:

- Summarization and drafting
- Pattern identification with provided context
- Template-based generation (reports, briefs, BOLOs)

They struggle with:

- Complex multi-step reasoning
- Nuanced legal or policy analysis
- Long document comprehension without additional tooling
- Tasks requiring broad world knowledge

This isn't a dealbreaker — it means setting realistic expectations. For many law enforcement analytical workflows, a local 8B model handles 80% of the work, and the analyst provides the judgment for the remaining 20%.

> **For full details:** See [Limitations and Tradeoffs](10-limitations-tradeoffs.md).

---

## When Local Deployment Makes Sense — and When It Doesn't

### Strong Case for Local Deployment

Local LLMs are the right choice when:

| Factor | Why Local Wins |
|--------|---------------|
| **Data sensitivity** | CJI, classified, or sensitive intelligence data that cannot leave your infrastructure |
| **Multi-user teams** | Cost advantage grows with each additional user (no per-seat licensing) |
| **Operational independence** | Need for AI in disconnected, field, or secure environments |
| **Regulatory requirements** | CJIS, IRS 1075, or other frameworks requiring on-premises data processing |
| **Institutional control** | Need to control model versions, updates, and configuration |
| **Budget constraints** | One-time capital expenditure preferred over ongoing operational costs |

### Weak Case for Local Deployment

Local LLMs may **not** be the right choice when:

| Factor | Why Cloud May Be Better |
|--------|------------------------|
| **Single user, non-sensitive data** | Cloud AI subscriptions ($20–$30/month) are cheaper than maintaining dedicated hardware |
| **Need for cutting-edge reasoning** | Complex legal analysis, multi-step reasoning — commercial models are significantly better at 70B+ scale |
| **Limited IT support** | No internal capability to troubleshoot, maintain, and update the system |
| **FedRAMP compliance already achieved** | If your agency already uses Azure Government with CJIS addendum, adding Azure AI may be simpler |
| **Rapid capability scaling** | Cloud scales instantly; local hardware requires procurement cycles |

### The Decision Framework

Ask these questions to determine which path fits your agency:

1. **Does your workload involve CJI or sensitive data?** → If yes, local deployment or FedRAMP-authorized cloud with CJIS addendum are your only options.
2. **How many users need AI capability?** → More than 3–5 users strengthens the local ROI case.
3. **Do you have IT staff who can support this?** → If not, cloud may be more practical despite higher per-user cost.
4. **What's your connectivity situation?** → Disconnected or intermittent environments require local deployment.
5. **What's your budget structure?** → Capital expenditure (one-time) vs. operational expenditure (recurring) — which does your procurement process favor?

---

## The Bottom Line

Local LLM deployment transforms AI from a security risk into a secure strategic asset. But it requires:

- **Honest expectations** about model capability at the 7B–13B scale
- **Deliberate security hardening** beyond the default installation
- **Institutional commitment** to maintenance, training, and policy development
- **Phased approach** — start with non-CJI workloads, prove value, then harden for sensitive data

The gap between "technically possible" and "CJIS compliant" is bridgeable with deliberate, phased effort. It is a "hardening required" scenario, not a "fundamentally incompatible" one.

---

## Next Steps

| Your Role | Start Here |
|-----------|------------|
| **Analyst / End User** | [Prompting Guide](06-prompting-guide.md) and [Use Cases](07-use-cases.md) |
| **IT / Security** | [CJIS Compliance Framework](09-cjis-compliance.md) and [Hardware Guide](04-hardware-guide.md) |
| **Leadership / Budget** | [Limitations and Tradeoffs](10-limitations-tradeoffs.md) and [Next Steps](12-next-steps.md) |
| **Ready to try it** | [Ollama Quickstart](03-ollama-quickstart.md) |

---

## References

- Executive Order 14110 — Safe, Secure, and Trustworthy Development and Use of Artificial Intelligence (October 2023)
- FBI CJIS Security Policy v6.0 — [CJIS Resource Center](https://www.fbi.gov/services/cjis)
- FedRAMP Authorization — [fedramp.gov](https://www.fedramp.gov/)

---

*This document is part of the [NFCA Open Source LLM](../README.md) companion resource. It is an educational resource, not official guidance. Consult your agency's CJIS Systems Officer (CSO) for compliance decisions.*
