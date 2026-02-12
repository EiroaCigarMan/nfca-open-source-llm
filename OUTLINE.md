# NFCA Open Source LLM — Repository Outline

**Purpose:** Companion resource for the "Secure and Affordable In-House AI" presentation. Provides the depth that a time-limited presentation cannot — particularly CJIS compliance mapping, honest limitations, and practical next steps for law enforcement professionals evaluating local LLM deployment.

**Audience:** Law enforcement intelligence professionals, fusion center analysts, agency IT/security teams considering local AI deployment.

**Format:** Minimal starter — markdown documentation with links to external resources. No runnable code or deployment configs.

---

## Repository Structure

```
nfca-open-source-llm/
|
|-- README.md                          # Landing page — presentation summary + repo navigation
|
|-- docs/
|   |-- 01-why-local-llms.md           # The case for local AI in law enforcement
|   |-- 02-understanding-llms.md       # How LLMs actually work — honest capabilities and limits
|   |-- 03-ollama-quickstart.md        # Getting started with Ollama (first 30 minutes)
|   |-- 04-hardware-guide.md           # Hardware requirements and honest cost analysis
|   |-- 05-model-selection.md          # Which models for which tasks (with specific versions)
|   |-- 06-prompting-guide.md          # Prompt engineering for law enforcement workflows
|   |-- 07-use-cases.md               # Real-world use cases with example prompts
|   |-- 08-knowledge-management.md     # PKM + RAG architecture explained
|   |-- 09-cjis-compliance.md          # CJIS Security Policy compliance framework
|   |-- 10-limitations-tradeoffs.md    # Honest limitations and when NOT to use local LLMs
|   |-- 11-security-considerations.md  # Security considerations for AI-generated output
|   |-- 12-next-steps.md              # Implementation roadmap and additional resources
|
|-- resources/
|   |-- prompt-templates/
|   |   |-- situational-awareness-brief.md
|   |   |-- incident-summary.md
|   |   |-- bolo-generator.md
|   |   |-- evidence-review.md
|   |   |-- pattern-analysis.md
|   |
|   |-- checklists/
|   |   |-- pre-deployment-checklist.md
|   |   |-- cjis-compliance-checklist.md
|   |   |-- hardware-procurement-checklist.md
|   |
|   |-- references/
|       |-- glossary.md
|       |-- external-links.md
|       |-- faq.md
|
|-- LICENSE
```

---

## Document-by-Document Outline

### README.md

- Presentation title, author, and context (NFCA audience)
- One-paragraph summary of the value proposition
- "Start Here" section directing readers to the most relevant doc for their role:
  - **Analysts:** `06-prompting-guide.md` and `07-use-cases.md`
  - **IT/Security:** `09-cjis-compliance.md` and `04-hardware-guide.md`
  - **Leadership:** `01-why-local-llms.md` and `10-limitations-tradeoffs.md`
- Table of contents linking all docs
- Disclaimer: Not official guidance; consult your agency CSO for compliance decisions

---

### docs/01-why-local-llms.md
**Expands on:** Slide 2 (Why Local LLMs Are a Game-Changer)
**Addresses gap from analysis:** Overclaimed benefits need honest framing

**Outline:**
1. **The Capability Gap** — AI tools are banned for CJI workloads, but the analytical need persists
   - Manual hours spent on summarization, pattern analysis, report drafting
   - Peer agencies deploying AI within their security boundaries
   - Executive Order 14110 directing federal AI adoption
2. **Three Value Drivers for Local Deployment**
   - Data sovereignty — all processing within your infrastructure
   - Cost predictability — fixed hardware vs. recurring subscriptions
   - Operational independence — no internet dependency, no vendor outages
3. **Honest Caveats** (from analysis)
   - Air-gap alone does not ensure compliance (addresses only data transmission)
   - Initial model download requires internet; true air-gap is post-setup
   - Total cost is higher than hardware alone (electricity, IT support, maintenance)
   - For single users doing non-sensitive work, cloud may be cheaper
4. **When Local Deployment Makes Sense vs. When It Doesn't**
   - Strong case: multi-user, high-sensitivity data, operational independence requirements
   - Weak case: single user, non-sensitive data, limited IT support

---

### docs/02-understanding-llms.md
**Expands on:** Slide 3 (Understanding Local LLMs)
**Addresses critical gap from analysis:** Hallucination and temperature overclaims

**Outline:**
1. **What LLMs Actually Are** — probabilistic text generation, not knowledge databases
   - Trained on data up to a cutoff date
   - Generate outputs word-by-word based on probability
   - Do not "know" things — they predict likely continuations
2. **Hallucination — The Critical Reality**
   - **Corrected claim:** Feeding data to a model *significantly reduces* hallucination but does NOT eliminate it
   - Models can still generate plausible-sounding but incorrect interpretations
   - Smaller models (7B-13B) hallucinate more frequently than larger models (70B+)
   - Even with perfect context, models can misattribute, misquote, or incorrectly synthesize
   - **Bottom line:** Every output must be reviewed by a human before use
3. **Temperature Settings — What They Actually Control**
   - **Corrected claim:** Temperature 0 makes outputs more *consistent and repeatable*, not more truthful
   - Does NOT mean the model only outputs facts from your data
   - Some implementations still use sampling even at temp=0
   - The model has no "factual mode" — it always generates probabilistically
   - Practical recommendation: Use temp=0 for consistency, but never skip human review
4. **What to Tell Your Audience When They Ask**
   - "If I feed it an incident report, will it make things up?" — Honest answer framework
   - "What's the error rate?" — How to benchmark in your environment
   - Setting realistic expectations vs. overselling

---

### docs/03-ollama-quickstart.md
**Expands on:** Slide 4 (Introducing Ollama)
**Addresses gap from analysis:** Missing alternatives context and GUI options

**Outline:**
1. **What Is Ollama** — Open-source local LLM runtime (MIT License)
   - Single binary, single command deployment
   - Free, no vendor lock-in
   - OpenAI-compatible API for tool integration
2. **First 30 Minutes Quickstart**
   - Step 1: Install Ollama (platform-specific instructions)
   - Step 2: Pull your first model (`ollama pull llama3.1:8b`)
   - Step 3: Run a test prompt
   - Step 4: See results
3. **Why Ollama Over Alternatives**
   - Brief comparison: LM Studio, LocalAI, vLLM, llama.cpp
   - Selection rationale: simplest on-ramp, broadest model library, active community
4. **GUI Options for Non-CLI Users**
   - Open WebUI (formerly Ollama Web UI) — ChatGPT-like interface
   - How to set up Open WebUI with Ollama
   - Why this matters: most analysts want a familiar chat interface
5. **Key Links**
   - Ollama official site and GitHub
   - Open WebUI project
   - Model library at ollama.com/library

---

### docs/04-hardware-guide.md
**Expands on:** Slide 5 (Hardware Requirements)
**Addresses gap from analysis:** Missing model-capability table and honest cost comparison

**Outline:**
1. **Recommended Hardware Specifications**
   - GPU: NVIDIA RTX 4060 Ti 16GB VRAM ($400-600)
   - RAM: 32GB DDR5 ($80-120)
   - CPU: Intel i5/i7 12th gen+ or AMD Ryzen 5/7 5000+ ($150-250)
   - Storage: 1TB NVMe SSD ($70-100)
   - PSU: 650-750W ($70-100)
   - Total: $1,000-1,500 hardware
2. **What Your Hardware Can Actually Run** (from analysis)
   - Model-capability table with VRAM requirements:
     - Llama 3.1 8B → ~6GB VRAM → fits on 16GB → good for drafting, basic analysis
     - Mistral 7B → ~5GB VRAM → fits on 16GB → good for general tasks
     - Llama 3.1 70B → ~40GB VRAM → does NOT fit → needs $2K+ GPU
     - Phi-3 Mini 3.8B → ~3GB VRAM → fits → lightweight, limited capability
     - Qwen3-8B → ~6GB VRAM → fits → strong multilingual, reasoning
3. **Honest Cost Comparison** (genericized from work proposal)
   - Local workstation (1 user): Year 1 ~$1,700, Year 2 ~$200, Year 3 ~$200
   - ChatGPT Team (1 user): $300/year
   - ChatGPT Team (10 users): $3,000/year
   - Cloud AI (10 users, govcloud): $5,000-10,000/year
   - **Key insight:** ROI strongest for multi-user or high-sensitivity data scenarios
4. **GPU Alternatives and Considerations**
   - AMD ROCm support: exists but less mature
   - Laptop deployment: possible with 8GB+ VRAM gaming laptops
   - Server deployment: for multi-user scenarios
5. **Hidden Costs to Budget For**
   - Electricity: ~$15-30/month under load
   - IT support time
   - Setup hours: 4-8 hours for competent tech
   - Model updates require manual effort on air-gapped systems

---

### docs/05-model-selection.md
**Expands on:** Slide 6 (Model Selection for Work Projects)
**Addresses gap from analysis:** Vague model names need exact versions and capability boundaries

**Outline:**
1. **Recommended Models with Exact Names**
   - Llama 3.1 8B-Instruct — `ollama pull llama3.1:8b`
   - Mistral 7B-Instruct v0.3 — `ollama pull mistral:7b`
   - Qwen3-8B — `ollama pull qwen3:8b`
   - Phi-3 Mini 3.8B — `ollama pull phi3:mini`
   - DeepSeek-R1-Distill 8B — `ollama pull deepseek-r1:8b`
2. **Model-Task Matching Guide**
   - Document analysis (summarization, extraction): Llama 3.1 8B, Qwen3-8B
   - Research synthesis (multi-source briefings): Mistral 7B, Qwen3-8B
   - Content drafting (reports, memos): Llama 3.1 8B
   - Reasoning tasks: DeepSeek-R1-Distill 8B
   - Lightweight/fast tasks: Phi-3 Mini
3. **What These Models Are BAD At** (from analysis — critical missing content)
   - Complex multi-step reasoning
   - Nuanced legal/policy analysis
   - Long document comprehension (>4K tokens without RAG)
   - Tasks requiring broad world knowledge
   - Mathematical calculations
   - At 7B-13B range, expect meaningful quality gap vs. GPT-4/Claude
4. **How to Evaluate Models for Your Workflow**
   - Test with representative tasks from your actual work
   - Use standardized prompts to compare models
   - Track accuracy over multiple runs
   - Document which model works best for which task

---

### docs/06-prompting-guide.md
**Expands on:** Slide 7 (Mastering Effective Prompts)
**Addresses gap from analysis:** Only shows concept, needs more practical examples and system prompt info

**Outline:**
1. **Core Principle:** You already know how to do your job — now write those steps for AI
2. **Prompt Structure Framework**
   - Role definition (AI personality)
   - Context (what data you're providing)
   - Task (what you want done)
   - Output format (how you want results structured)
   - Constraints (what to avoid)
3. **System Prompts vs. User Prompts**
   - What system prompts are and why they matter
   - How to use Ollama Modelfiles to save system prompts
   - Example Modelfile for a law enforcement analyst persona
4. **Domain-Specific Prompt Templates**
   - Situational awareness brief (expanded from Slide 8)
   - Crime pattern analysis
   - BOLO generation
   - Evidence summary/review
   - Intelligence product drafting
5. **Prompt Libraries and Sharing**
   - How to save prompts as Modelfiles in Ollama
   - Sharing effective prompts across analyst teams
   - Iterating on prompts: expect 2-4 weeks to become proficient
6. **Common Prompting Mistakes in LE Context**
   - Being too vague ("summarize this")
   - Not specifying output format
   - Not including the review/verification step
   - Assuming the model "knows" your agency's SOPs

---

### docs/07-use-cases.md
**Expands on:** Slide 8 (Use Case: Situational Awareness Brief)
**Addresses gap from analysis:** Needs evidence basis and more examples

**Outline:**
1. **Use Case 1: Monday Morning Situational Awareness Brief**
   - Scenario description
   - Full prompt template
   - Expected output structure
   - Honest assessment: "In testing, this reduced a typical 2-hour process to roughly 15 minutes of review and editing"
   - The 80/20 rule: first draft ~80% usable, 15 minutes for human judgment on the 20%
2. **Use Case 2: Case File Summarization**
   - Scenario: 200-page case file needs executive summary
   - Approach: chunk and summarize, then synthesize
   - Limitations: context window constraints at 7B-13B level
3. **Use Case 3: Training Scenario Generation**
   - Non-CJI use case — synthetic data only
   - Prompt template for realistic training scenarios
4. **Use Case 4: Policy/Procedure Drafting**
   - Administrative documents using public templates
   - Non-CJI safe use case
5. **Use Case 5: Open Source Intelligence Synthesis**
   - Public records, court records, news articles
   - Non-CJI by definition
6. **Questions Your Audience Will Ask** (from analysis)
   - "Can I use this for official reports that go to the DA's office?"
   - "What if it gets something wrong in the brief?"
   - "Does this work with our RMS/CAD system?"
   - Honest answers for each

---

### docs/08-knowledge-management.md
**Expands on:** Slide 9 (Information Management: Your Second Brain)
**Addresses BIGGEST technical gap from analysis:** RAG architecture never explained

**Outline:**
1. **The Problem Slide 9 Introduces But Doesn't Explain**
   - "Feed your data to the LLM" — but how?
   - The missing bridge between Obsidian and Ollama
2. **What Is RAG (Retrieval Augmented Generation)**
   - Simple explanation for non-technical audience
   - Architecture diagram:
     ```
     Your Documents (Obsidian vault / files)
             |
        [Embedding / Indexing]
             |
        [Vector Search] <-- User Query
             |
        [Relevant Context]
             |
        [Ollama LLM] --> Response
     ```
   - Why this matters: transforms "general AI" into "AI that knows your data"
3. **Current Reality: Manual Copy-Paste Workflow**
   - Today's practical approach: copy relevant content into the prompt
   - Advantages: simple, no additional tooling
   - Limitations: manual effort, context window limits
4. **The Roadmap: Automated RAG Integration**
   - Obsidian plugins for LLM integration
   - Open WebUI document upload features
   - Dedicated RAG tools (AnythingLLM, PrivateGPT)
5. **Personal Knowledge Management with Obsidian**
   - Why Obsidian for LE professionals
   - Building searchable knowledge repositories
   - Linking cases, patterns, and intelligence
   - All data remains local — no cloud sync required
6. **Key Links**
   - Obsidian official site
   - Open WebUI RAG documentation
   - AnythingLLM project

---

### docs/09-cjis-compliance.md
**Addresses CRITICAL gap from analysis + integrates ollama-work-proposal.md content**
**Priority: CRITICAL — "the first thing this audience will ask about"**

**Outline:**
1. **Why CJIS Compliance Matters for Local LLMs**
   - Air-gap alone does NOT ensure compliance
   - CJIS has 13 policy areas — air-gap addresses only data transmission
   - "Consult your agency CJIS Systems Officer (CSO) for full compliance assessment"
2. **CJIS Security Policy v6.0 — Full 13-Area Mapping**
   (Genericized from work proposal Section 4.1)
   - Table mapping each of 13 CJIS policy areas to:
     - Default Ollama compliance status
     - What's needed for compliance
     - Which deployment phase addresses it
3. **The 8 Application-Layer Gaps** (genericized from work proposal Section 4.2)
   - Gap 1: Authentication (CJIS 5.6) — Ollama has no auth by default
   - Gap 2: Authorization/RBAC (CJIS 5.9) — all users have identical access
   - Gap 3: Audit Logging (CJIS 5.7) — no prompt/response logging
   - Gap 4: Encryption in Transit (CJIS 5.10) — HTTP only by default
   - Gap 5: Encryption at Rest (CJIS 5.10) — plaintext KV cache files
   - Gap 6: Incident Response (CJIS 5.2) — no anomaly detection
   - Gap 7: Configuration Management (CJIS 5.13) — no security baseline
   - Gap 8: Media Protection (CJIS 5.11) — no cache sanitization
   - For each gap: what CJIS requires, what Ollama provides, why infrastructure doesn't fix it, remediation direction
4. **The Infrastructure vs. Application Security Distinction**
   (from work proposal Section 4.4)
   - "A bank vault with unlocked filing cabinets" analogy
   - Infrastructure layer (what your agency already provides) vs. Application layer (what Ollama needs)
   - Diagram showing where controls end and gaps begin
5. **Phased Compliance Approach**
   (genericized from work proposal Section 5)
   - Phase 1: Non-CJI pilot (zero CJI risk)
   - Phase 2: Security hardening (8 application-layer controls)
   - Phase 3: Compliance validation (ATO)
   - Phase 4: CJI production (only after ATO)
   - Key principle: CJI is NEVER processed on an unhardened system
6. **Data Loss Prevention for Non-CJI Phase**
   - Why DLP matters even in a "non-CJI" pilot
   - Microsoft Presidio overview (open source DLP)
   - CJI pattern detection capabilities
   - Preventing accidental CJI leakage
7. **Known Vulnerabilities — CVE Awareness**
   (from work proposal Section 4.3 — this is public information)
   - Summary table of known Ollama CVEs
   - Why proactive disclosure builds trust
   - Mitigation strategies for each
8. **Key CJIS Compliance Takeaways**
   - Default Ollama satisfies ~5 of 13 CJIS areas
   - With deliberate hardening, all 13 are addressable
   - The gap is "hardening required," not "fundamentally incompatible"
   - Never deploy CJI workloads before completing compliance validation

---

### docs/10-limitations-tradeoffs.md
**Addresses HIGH priority gap from analysis: no honest limitations discussion**

**Outline:**
1. **Capability Limitations at 7B-13B Scale**
   - Less capable than GPT-4/Claude for complex reasoning
   - Struggle with multi-step reasoning, nuanced legal analysis
   - Long document comprehension limited without RAG
   - Mathematical calculations unreliable
   - Broad world knowledge more limited than large commercial models
2. **Operational Limitations**
   - Learning curve: expect 2-4 weeks to become proficient
   - Model updates require manual download and deployment
   - No built-in collaboration features (single workstation)
   - Air-gapped updates require sneakernet (CDS transfer)
3. **When NOT to Use Local LLMs**
   - When you need commercial-grade reasoning (complex legal analysis)
   - When you have unlimited budget and simple compliance path (Azure AI may be better)
   - When a single user does non-sensitive work (cloud is cheaper)
   - When you lack IT support for maintenance
4. **The Honest Cloud Comparison**
   - Azure OpenAI with FedRAMP High: pre-built compliance, lower total cost
   - Trade-off: data sovereignty vs. compliance engineering cost
   - This is not "local is always better" — it depends on your requirements
5. **What to Tell Skeptics**
   - "I tried ChatGPT and it made things up. Why would this be different?" — Honest answer
   - "Is this just a fad?" — LLM technology trajectory
   - "Who supports this if something breaks?" — Community support reality

---

### docs/11-security-considerations.md
**Addresses HIGH priority gap from analysis: legal/policy considerations for AI output**

**Outline:**
1. **AI-Generated Content Review Policy**
   - All AI output must be reviewed before dissemination
   - AI is a drafting tool, not an author — human reviewer is always accountable
   - Establish minimum review standards by document type
2. **Disclosure Requirements**
   - "AI-assisted analysis" notation on products
   - When and how to disclose AI use in official documents
   - Agency policy recommendations
3. **Legal Considerations**
   - Brady/discovery implications for AI-drafted investigative documents
   - Evidentiary considerations for AI-assisted analysis
   - Retention policies for AI interactions (prompts and responses)
4. **Departmental Policy Framework**
   - Template for AI acceptable use policy
   - Roles and responsibilities (who reviews, who approves)
   - Training requirements for AI users
5. **Incident Response for AI Misuse**
   - What constitutes misuse (feeding unauthorized data, relying without review)
   - Reporting procedures
   - Remediation steps

---

### docs/12-next-steps.md
**Expands on:** Slide 10 (Key Takeaways & Next Steps)

**Outline:**
1. **Implementation Roadmap Summary**
   - Phase 1: Non-CJI pilot (4 weeks, minimal cost, zero compliance risk)
   - Phase 2: Security hardening (if Phase 1 succeeds)
   - Phase 3: Compliance validation
   - Phase 4: CJI production (only after ATO)
2. **"What I'd Do Differently" — Lessons Learned**
   - From the presenter's experience
   - Common pitfalls to avoid
3. **Budget Request Template**
   - Line item justification language for procurement
   - How to frame this for your chief/director
   - "My chief will ask why not just use Microsoft Copilot" — comparison points
4. **Training and Onboarding Plan**
   - Getting your first analyst productive
   - Building an internal champion program
   - Sharing prompts and best practices across the team
5. **Additional Resources**
   - Links to Ollama, Open WebUI, Presidio, and other referenced tools
   - FBI CJIS Security Policy resource center
   - Government AI guidance (EO 14110, FedRAMP)
   - Community forums and support channels

---

### resources/prompt-templates/

Each template follows the same format:
- **Scenario:** When to use this prompt
- **Prerequisites:** What data you need to prepare
- **The Prompt:** Ready to copy-paste
- **Expected Output:** What you should get back
- **Review Checklist:** What to verify before using the output

**Templates:**
1. `situational-awareness-brief.md` — Monday morning brief from weekend incidents
2. `incident-summary.md` — Single incident executive summary
3. `bolo-generator.md` — Be On the Lookout bulletin drafting
4. `evidence-review.md` — Evidence list synthesis and gap identification
5. `pattern-analysis.md` — Cross-case pattern identification

---

### resources/checklists/

1. `pre-deployment-checklist.md` — Everything to verify before first Ollama deployment
2. `cjis-compliance-checklist.md` — 13-area CJIS compliance verification (genericized from work proposal)
3. `hardware-procurement-checklist.md` — What to include in your hardware purchase request

---

### resources/references/

1. `glossary.md` — Terms and acronyms (from work proposal Appendix G, expanded)
2. `external-links.md` — Curated links to all referenced tools and documentation
3. `faq.md` — Frequently asked questions with honest answers (from analysis Section: Potential Audience Questions)

---

## Content Sourcing Map

| Document | Primary Source | Secondary Source |
|----------|---------------|-----------------|
| 01-why-local-llms | Slide 2 | Work proposal Section 2 |
| 02-understanding-llms | Slide 3 | NFCA analysis (critical corrections) |
| 03-ollama-quickstart | Slide 4 | Work proposal Section 3.1 |
| 04-hardware-guide | Slide 5 | NFCA analysis (missing tables) |
| 05-model-selection | Slide 6 | NFCA analysis (exact model names) |
| 06-prompting-guide | Slide 7 | NFCA analysis (more examples needed) |
| 07-use-cases | Slide 8 | NFCA analysis (evidence basis) |
| 08-knowledge-management | Slide 9 | NFCA analysis (RAG architecture gap) |
| 09-cjis-compliance | NEW | Work proposal Section 4 (genericized) |
| 10-limitations-tradeoffs | NEW | NFCA analysis + work proposal Section 6 |
| 11-security-considerations | NEW | NFCA analysis (legal/policy gap) |
| 12-next-steps | Slide 10 | Work proposal Sections 5, 10 |

---

## Disclaimers for README.md

- This repository is an educational resource, not official guidance
- Consult your agency's CJIS Systems Officer (CSO) for compliance decisions
- No classified or CJI data should ever be committed to this repository
- The author is a law enforcement professional sharing practical experience, not a vendor
- CJIS compliance analysis is based on publicly available policy documents and should be independently validated by your security team
