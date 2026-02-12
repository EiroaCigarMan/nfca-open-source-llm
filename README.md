# Secure and Affordable In-House AI

**A Practical Guide to Local LLM Deployment for Law Enforcement**

Companion resource for the "Secure and Affordable In-House AI: The Strategic Imperative for Local LLMs" presentation delivered to the National Fusion Center Association (NFCA) and law enforcement intelligence professionals.

This repository provides the depth that a time-limited presentation cannot -- particularly CJIS Security Policy compliance mapping, honest limitations, and practical next steps for agencies evaluating local AI deployment.

---

## Start Here

**Choose your path based on your role:**

| Your Role | Start With | Then Read |
|-----------|-----------|-----------|
| **Analyst** | [Prompting Guide](docs/06-prompting-guide.md) | [Use Cases](docs/07-use-cases.md), [Prompt Templates](resources/prompt-templates/) |
| **IT / Security** | [CJIS Compliance](docs/09-cjis-compliance.md) | [Hardware Guide](docs/04-hardware-guide.md), [Limitations](docs/10-limitations-tradeoffs.md) |
| **Leadership** | [Why Local LLMs](docs/01-why-local-llms.md) | [Limitations & Tradeoffs](docs/10-limitations-tradeoffs.md), [Next Steps](docs/12-next-steps.md) |

---

## Table of Contents

### Core Documentation

| # | Document | Description |
|---|----------|-------------|
| 01 | [Why Local LLMs](docs/01-why-local-llms.md) | The case for local AI in law enforcement -- capability gap, value drivers, and honest caveats |
| 02 | [Understanding LLMs](docs/02-understanding-llms.md) | How LLMs actually work -- correcting common misconceptions about hallucination and determinism |
| 03 | [Ollama Quickstart](docs/03-ollama-quickstart.md) | Getting started with Ollama in your first 30 minutes |
| 04 | [Hardware Guide](docs/04-hardware-guide.md) | Hardware requirements, model-capability mapping, and honest cost analysis |
| 05 | [Model Selection](docs/05-model-selection.md) | Which models for which tasks -- with exact versions and pull commands |
| 06 | [Prompting Guide](docs/06-prompting-guide.md) | Prompt engineering for law enforcement workflows |
| 07 | [Use Cases](docs/07-use-cases.md) | Real-world use cases with example prompts and honest assessments |
| 08 | [Knowledge Management](docs/08-knowledge-management.md) | Personal knowledge management, RAG architecture, and connecting your data to LLMs |
| 09 | [CJIS Compliance](docs/09-cjis-compliance.md) | CJIS Security Policy v6.0 compliance framework -- 13-area mapping and 8 application-layer gaps |
| 10 | [Limitations & Tradeoffs](docs/10-limitations-tradeoffs.md) | Honest limitations and when NOT to use local LLMs |
| 11 | [Security Considerations](docs/11-security-considerations.md) | Legal, policy, and evidentiary considerations for AI-generated output |
| 12 | [Next Steps](docs/12-next-steps.md) | Implementation roadmap, budget guidance, and additional resources |

### Resources

| Resource | Description |
|----------|-------------|
| [Prompt Templates](resources/prompt-templates/) | Ready-to-use prompt templates for common LE analytical tasks |
| [Checklists](resources/checklists/) | Pre-deployment, CJIS compliance, and hardware procurement checklists |
| [Glossary](resources/references/glossary.md) | Terms and acronyms used throughout this guide |
| [External Links](resources/references/external-links.md) | Curated links to all referenced tools and documentation |
| [FAQ](resources/references/faq.md) | Frequently asked questions with honest answers |

---

## Key Takeaways

1. **Local LLMs solve a real problem.** AI tools are banned for CJI workloads, but the analytical need persists. Local deployment keeps data within your infrastructure.

2. **Air-gap alone is not compliance.** CJIS has 13 policy areas. Default Ollama satisfies approximately 5. The gap is "hardening required," not "fundamentally incompatible." See the [CJIS Compliance](docs/09-cjis-compliance.md) section.

3. **Set realistic expectations.** Local models at the 7B-13B parameter range are useful tools, not magic. They hallucinate, they have limits, and every output requires human review. See [Understanding LLMs](docs/02-understanding-llms.md).

4. **Start with non-CJI workloads.** A non-CJI pilot carries zero compliance risk and lets you prove value before committing to security hardening. See [Next Steps](docs/12-next-steps.md).

---

## Important Disclaimers

- This repository is an **educational resource**, not official guidance from any agency or organization
- **Consult your agency's CJIS Systems Officer (CSO)** for all compliance decisions
- No classified or CJI data should ever be committed to or referenced in this repository
- The CJIS compliance analysis is based on publicly available policy documents and should be independently validated by your security team
- The author is a law enforcement professional sharing practical experience, not a vendor or consultant

---

## Quick Links

| Resource | URL |
|----------|-----|
| Ollama | https://ollama.com |
| Ollama GitHub | https://github.com/ollama/ollama |
| Open WebUI | https://github.com/open-webui/open-webui |
| FBI CJIS Security Policy | https://www.fbi.gov/services/cjis/cjis-security-policy-resource-center |
| Microsoft Presidio (DLP) | https://github.com/microsoft/presidio |
| LiteLLM Proxy | https://docs.litellm.ai |

---

## License

This work is shared for educational purposes within the law enforcement community. See [LICENSE](LICENSE) for details.
