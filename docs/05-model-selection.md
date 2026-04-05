# Model Selection for Law Enforcement Workflows

> **Summary:** Not all models are equal, and not all tasks require the same model. This guide documents the models actively deployed in production at the Dallas Fusion Center / Real-Time Crime Center -- with exact pull commands, honest capability assessments, VRAM requirements, training data cutoff dates, and task-matching recommendations. The goal is to help practitioners choose the right tool for the right job, avoid wasting time on models that do not fit their hardware, and set realistic expectations about what these models can and cannot do.

---

## Table of Contents

- [How to Read This Guide](#how-to-read-this-guide)
- [Deployed Models](#deployed-models)
- [Quick Reference Table](#quick-reference-table)
- [Model Profiles](#model-profiles)
- [Model-Task Matching Guide](#model-task-matching-guide)
- [What These Models Are Bad At](#what-these-models-are-bad-at)
- [How to Evaluate Models for Your Workflow](#how-to-evaluate-models-for-your-workflow)
- [Quantization Explained](#quantization-explained)
- [Running Multiple Models](#running-multiple-models)
- [References](#references)

---

## How to Read This Guide

Every model listed below includes:

- **Exact Ollama pull command** -- copy-paste ready
- **Parameter count** -- how large the model is (bigger generally means more capable but slower)
- **Approximate VRAM required** -- how much GPU memory the model needs
- **Fits on 16GB GPU?** -- whether the model runs on the recommended RTX 4060 Ti 16GB from the [Hardware Guide](04-hardware-guide.md)
- **Training Data Cutoff** -- the approximate date through which the model's training data was collected; events after this date are outside the model's knowledge
- **Best use cases** -- what the model does well
- **Key strengths** -- where the model outperforms peers
- **Key weaknesses** -- where the model falls short
- **Honest assessment** -- practical observations from real-world testing

VRAM estimates reflect the specific quantization used in this deployment (noted per model). Actual VRAM usage varies by context length and concurrent sessions.

---

## Deployed Models

### Quick Reference Table

| Model | Developer | Parameters | VRAM (approx.) | Fits 16GB GPU? | Primary Use Case |
|-------|-----------|-----------|----------------|----------------|------------------|
| [Gemma 4 (E4B)](#gemma-4-e4b) | Google DeepMind | 4.5B (MoE) | ~10 GB | Yes | Multimodal general-purpose workhorse |
| [DeepSeek-R1 14B](#deepseek-r1-14b) | DeepSeek AI | 14B | ~9 GB | Yes | Chain-of-thought reasoning |
| [GPT-OSS 20B](#gpt-oss-20b) | OpenAI | 20.9B (MoE) | ~13 GB | Yes | Agentic tool use and structured output |
| [Phi-4 Mini](#phi-4-mini) | Microsoft | 3.8B | ~3 GB | Yes | Fast lightweight tasks |
| [Granite 4 Micro](#granite-4-micro) | IBM | 3B | ~2 GB | Yes | Enterprise RAG and tool calling |
| [Nemotron-Cascade-2](#nemotron-cascade-2) | NVIDIA | 30B (MoE) | ~24 GB | No | Advanced reasoning, 256K context |
| [GLM-4.7-Flash](#glm-47-flash) | Z.ai / ZhipuAI | 30B (MoE) | ~31 GB | No | Bilingual Chinese-English reasoning |

**Starting recommendation:** Begin with **Gemma 4 (E4B)** for most analytical tasks. Add **DeepSeek-R1 14B** for pattern analysis and reasoning tasks. Use **GPT-OSS 20B** when structured output and tool calling are required.

---

## Model Profiles

### Gemma 4 (E4B)

```bash
ollama pull gemma4:latest
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | Google DeepMind |
| **Parameters** | 4.5B effective (MoE architecture, E4B variant) |
| **VRAM Required** | ~10 GB |
| **Fits 16GB GPU** | Yes |
| **Context Window** | 128K tokens |
| **License** | Apache 2.0 (fully permissive) |
| **Training Data Cutoff** | January 2025 |

**Best Use Cases:**
- Document summarization and analytical writing
- Image-plus-text tasks -- analyzing charts, diagrams, or evidence photos alongside narrative context
- General drafting: briefings, BOLOs, memos
- Agentic workflows requiring a capable lightweight model

**Key Strengths:**
- **Multimodal** -- accepts both text and image input; no other model in this deployment shares this capability
- Most recent training cutoff (January 2025) in the hardware-compatible tier -- best knowledge currency for current events
- MoE architecture delivers efficient performance relative to VRAM usage
- Apache 2.0 license eliminates licensing questions for government deployment
- Google DeepMind's alignment work produces reliable instruction following

**Key Weaknesses:**
- E4B is the smallest Gemma 4 variant; larger variants (12B, 27B) provide greater capability but require more VRAM
- MoE architecture loads more total parameters than its 4.5B effective count suggests; VRAM usage (~10 GB) is higher than a comparable dense 4.5B model
- Image processing consumes additional context window capacity

**Honest Assessment:** Gemma 4 E4B is the default recommendation for most law enforcement analytical workflows in this deployment. Its multimodal capability opens use cases unavailable to text-only models: extracting text from evidence photos, reviewing surveillance stills alongside narrative reports, or summarizing image-heavy documents. The January 2025 training cutoff -- the most current among hardware-compatible models here -- reduces the risk of outdated responses on recent events. Start here; escalate to DeepSeek-R1 14B or GPT-OSS 20B when tasks require deeper reasoning or structured output.

---

### DeepSeek-R1 14B

```bash
ollama pull deepseek-r1:14b
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | DeepSeek AI (China) |
| **Parameters** | 14B (dense; distilled from DeepSeek-R1 671B) |
| **VRAM Required** | ~9 GB |
| **Fits 16GB GPU** | Yes |
| **Context Window** | 128K tokens |
| **License** | MIT License |
| **Training Data Cutoff** | ~July 2024 |

**Best Use Cases:**
- Pattern analysis requiring step-by-step logical reasoning
- Evidence synthesis and contradiction detection across multiple source documents
- Timeline reconstruction where the reasoning process needs to be auditable
- Complex analytical tasks where understanding *how* the model reached a conclusion matters

**Key Strengths:**
- Chain-of-thought reasoning -- outputs its thinking process before the final answer, making logic auditable
- Distilled from DeepSeek-R1 671B, the 14B scale retains significantly stronger reasoning than 8B alternatives
- MIT license -- permissive for government deployment
- Fits on standard 16GB hardware while providing meaningfully better analytical depth than smaller models
- Transparent reasoning traces make it easier to identify where the model went wrong

**Key Weaknesses:**
- Developed by a Chinese organization (DeepSeek AI) -- some agencies may have procurement or policy concerns about model provenance; note that all inference runs locally and no data is transmitted to external servers, and model weights are open-source and independently auditable
- Reasoning trace increases response time and token usage compared to direct-answer models
- Training cutoff of July 2024 may produce outdated responses on post-cutoff events
- Less optimized for simple drafting tasks where chain-of-thought reasoning adds unnecessary overhead

**Honest Assessment:** DeepSeek-R1 14B is the strongest reasoning model in the hardware-compatible tier. For law enforcement analytical tasks -- pattern recognition, evidence synthesis, timeline analysis -- its chain-of-thought approach produces more transparent and auditable outputs. The 14B scale over smaller 8B distill variants provides noticeably better analytical depth. The reasoning trace also functions as a form of analytical provenance: reviewers can follow the model's logic rather than evaluating only the conclusion. The provenance concern applies here as with other Chinese-origin models: evaluate against your agency's specific procurement policies before production deployment.

---

### GPT-OSS 20B

```bash
ollama pull gpt-oss:20b
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | OpenAI |
| **Parameters** | 20.9B total / 3.6B active (MoE architecture) |
| **VRAM Required** | ~13 GB |
| **Fits 16GB GPU** | Yes (with limited headroom) |
| **Context Window** | 128K tokens |
| **License** | Apache 2.0 (fully permissive) |
| **Training Data Cutoff** | June 2024 |

**Best Use Cases:**
- Agentic workflows requiring native function calling and tool use
- Structured data extraction with precise output formatting (JSON, tables, templated reports)
- Automated multi-step analytical pipelines
- Workflows where consistent, machine-readable output format is critical

**Key Strengths:**
- **Designed for agentic use** -- native function calling and tool-use capabilities are built into the model's training, not bolted on after the fact
- MoE architecture delivers 20B-level analytical capability at ~3.6B active parameter inference speed
- Apache 2.0 license -- fully permissive, no government restrictions
- OpenAI's alignment work produces reliable instruction following and structured output compliance
- Handles multi-turn, tool-calling workflows more reliably than instruction-tuned general models

**Key Weaknesses:**
- 13 GB VRAM usage leaves limited headroom on a 16GB GPU for very long context tasks -- keep prompts concise for stability
- MoE architecture requires all 20.9B parameters to reside in VRAM during inference, despite only 3.6B being active at any time
- Tool-calling capabilities require a compatible interface (Open WebUI or similar) to fully utilize
- Fewer community fine-tuned variants than models from the Llama ecosystem

**Honest Assessment:** GPT-OSS 20B is the best choice for structured analytical workflows and automation. Its native tool-use design makes it the strongest performer for function-calling pipelines -- for example, triggering lookups against local databases, formatting outputs in specific agency templates, or chaining tasks together automatically. For simple drafting and summarization, Gemma 4 E4B is lighter on VRAM and easier to work with. GPT-OSS 20B becomes the right choice when the output format and downstream automation matter as much as the content itself. The tight VRAM fit requires care: limit concurrent context length to stay under 16 GB.

---

### Phi-4 Mini

```bash
ollama pull phi4-mini:latest
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | Microsoft |
| **Parameters** | 3.8B (dense) |
| **VRAM Required** | ~3 GB |
| **Fits 16GB GPU** | Yes (significant headroom remaining) |
| **Context Window** | 128K tokens |
| **License** | MIT License |
| **Training Data Cutoff** | June 2024 |

**Best Use Cases:**
- Quick summaries of short documents
- Rapid question-answering for simple analytical queries
- First-pass drafts for short analytical products that will be reviewed and edited
- Environments with limited GPU resources (laptops, shared workstations)

**Key Strengths:**
- Extremely fast inference -- near-instant responses for most prompts
- ~3 GB VRAM usage allows running alongside other applications or models simultaneously
- MIT license -- maximum legal clarity for government use
- 128K context window despite its small parameter count
- Microsoft's "phi" training philosophy emphasizes reasoning capability per parameter, making it more capable than older 7B models for simple tasks

**Key Weaknesses:**
- Noticeably less capable than 14B or 20B models for complex analytical tasks
- More prone to hallucination than larger models in this deployment
- Struggles with multi-part instructions or nuanced analytical reasoning
- Output quality degrades for longer or more complex products

**Honest Assessment:** Phi-4 Mini is the right model when speed and resource efficiency are the priority. For quick document checks, short summaries, and situations where a fast first draft is sufficient, it outperforms models three times its size in those dimensions. Do not use it for complex pattern analysis, evidence synthesis, or tasks where hallucination risk has downstream consequences. For most full analytical workflows in this deployment, Gemma 4 E4B produces better results with acceptable speed. Phi-4 Mini's value is the ~3 GB footprint: it can run persistently in the background or on constrained hardware where larger models cannot.

---

### Granite 4 Micro

```bash
ollama pull granite4:latest
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | IBM |
| **Parameters** | 3B (dense; "Micro" variant) |
| **VRAM Required** | ~2 GB |
| **Fits 16GB GPU** | Yes (leaves >14 GB free) |
| **Context Window** | 128K tokens |
| **License** | Apache 2.0 (fully permissive) |
| **Training Data Cutoff** | Not publicly disclosed (estimated 2024–2025) |

**Best Use Cases:**
- RAG (Retrieval-Augmented Generation) workflows where the model grounds answers in retrieved documents
- Structured question-answering over provided context
- Tool calling and function execution in automated pipelines
- Multilingual document handling (12 supported languages including Spanish, French, Arabic, German)

**Key Strengths:**
- Purpose-built for enterprise workflows: RAG, tool use, summarization, and multilingual tasks
- **~2 GB VRAM footprint** -- the lowest of any model in this deployment; can run as a persistent background service without disrupting primary workloads
- IBM's enterprise focus produces conservative, well-structured output behavior appropriate for formal analytical products
- Apache 2.0 license -- no restrictions for government deployment
- 12-language support is a genuine differentiator for fusion center work involving multilingual source material

**Key Weaknesses:**
- At 3B parameters, complex analytical depth is limited compared to 14B+ models in this deployment
- Training data cutoff is not publicly disclosed by IBM -- knowledge currency is uncertain; treat as approximate and verify time-sensitive outputs
- IBM Granite community documentation is less extensive than Google or Microsoft model ecosystems
- Not optimized for creative or nuanced prose writing

**Honest Assessment:** Granite 4 Micro is the most enterprise-focused model in this deployment, reflecting IBM's priority on structured business workflows over general-purpose generation. Its ~2 GB VRAM footprint means it can run persistently as a dedicated RAG or tool-calling service while larger models handle primary analytical tasks. For agencies building multi-model pipelines -- one model for drafting, one for reasoning, one for structured extraction -- Granite 4 Micro is a strong candidate for the extraction and retrieval role. The undisclosed training cutoff is a limitation: for tasks requiring current knowledge, prefer Gemma 4 E4B (January 2025 cutoff) over Granite for the context lookup step.

---

### Nemotron-Cascade-2

```bash
ollama pull nemotron-cascade-2:latest
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | NVIDIA |
| **Parameters** | 30B total / 3B active (MoE; hybrid Transformer + Mamba-2 architecture) |
| **VRAM Required** | ~24 GB |
| **Fits 16GB GPU** | No -- requires 24GB+ VRAM (e.g., RTX 3090, RTX 4090, A5000, or equivalent) |
| **Context Window** | 262,144 tokens (256K) |
| **License** | NVIDIA Open Model License |
| **Training Data Cutoff** | ~June 2025 (approximate) |

**Best Use Cases:**
- Complex analytical reasoning beyond the capability of 8B-14B models
- Very long document analysis leveraging the 256K context window -- case files, lengthy intelligence reports, multi-document batches in a single pass
- Advanced agentic tasks requiring multi-step planning and execution
- Agencies that have outgrown 14B models but cannot support 70B hardware

**Key Strengths:**
- **256K token context window** -- by far the largest in this deployment; can process a full case file or multi-document intelligence package in a single pass without chunking
- Most recent training cutoff (~June 2025) of any model in this deployment -- best knowledge currency
- Hybrid Mamba-2 architecture processes long contexts efficiently without the quadratic cost of standard attention models
- Supports both "thinking" (chain-of-thought) and "instruct" (direct answer) modes -- configurable per task
- 30B parameter scale provides substantially better analytical depth than hardware-compatible 14B alternatives

**Key Weaknesses:**
- Requires 24GB+ VRAM -- does not run on the standard 16GB RTX 4060 Ti from the [Hardware Guide](04-hardware-guide.md); requires RTX 3090, RTX 4090, A5000, or workstation-class GPU
- **NVIDIA Open Model License is more restrictive than Apache 2.0 or MIT** -- review license terms before production deployment; consult your legal team, as the license includes restrictions on using the model to compete with NVIDIA products
- Hybrid Mamba-2 architecture has less community tooling and compatibility documentation than standard transformer models
- Hardware requirement is 2-3x the cost of the starter workstation build

**Honest Assessment:** Nemotron-Cascade-2 is the most capable model in this deployment and the right choice when analytical quality and context length matter more than hardware simplicity. Its 256K context window and ~June 2025 training cutoff represent genuine operational advantages for complex fusion center work. Agencies that have proved the value of local LLMs with 14B models and are ready to invest in 24GB+ GPU hardware should evaluate this model first. The license requires review before production use -- NVIDIA's Open Model License is permissive for most use cases but contains terms that require sign-off in some agency contexts. See the [Hardware Guide](04-hardware-guide.md) for GPU options that support 24GB+ VRAM requirements.

---

### GLM-4.7-Flash

```bash
ollama pull glm-4.7-flash:q8_0
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | Z.ai / ZhipuAI (China) |
| **Parameters** | 30B total / 3B active (MoE architecture) |
| **VRAM Required** | ~31 GB (Q8_0 quantization) |
| **Fits 16GB GPU** | No -- requires 32GB+ VRAM (workstation-class GPU: A100, RTX 6000 Ada, or equivalent) |
| **Context Window** | 198,000 tokens (~198K) |
| **License** | MIT License |
| **Training Data Cutoff** | ~Mid-to-late 2024 (approximate; not officially disclosed) |

**Best Use Cases:**
- Chinese-English bilingual document analysis and cross-language synthesis
- Reasoning-intensive tasks requiring MoE-level analytical capability
- Tool use and coding tasks in multi-language workflows
- Analytical work involving Chinese-language source material

**Key Strengths:**
- **Native Chinese-English bilingual capability** -- designed for bilingual workflows, not just translation; particularly valuable for intelligence work involving Chinese-language sources
- Interleaved Thinking mode provides chain-of-thought reasoning with transparent logic output alongside the final answer
- MIT license -- permissive for government deployment
- ~198K context window supports large document packages in a single pass
- MoE architecture delivers 30B-level reasoning at 3B active parameter inference speed

**Key Weaknesses:**
- **Requires 32GB+ VRAM** -- the Q8_0 quantization at ~31 GB does not fit on any single consumer GPU under 32 GB; requires enterprise-class hardware (A100, RTX 6000 Ada, dual-GPU consumer setup, or equivalent)
- Developed by a Chinese organization (ZhipuAI) -- the same provenance considerations apply as with DeepSeek-R1; all inference runs locally with no data transmission, and weights are open-source, but agencies with policies governing Chinese-origin software should review before deployment
- Training data cutoff is not officially disclosed in English documentation -- treat as approximate and unverified
- Highest hardware requirement of any model in this deployment; may not be operable on current infrastructure without GPU upgrade

**Honest Assessment:** GLM-4.7-Flash requires more substantial hardware than the rest of this deployment. Its Q8_0 quantization at ~31 GB places it firmly in the enterprise GPU tier. For agencies with 32GB+ VRAM hardware, its bilingual Chinese-English capability and Interleaved Thinking mode provide genuine analytical value for intelligence work involving Chinese-language source material -- a capability no other model in this deployment shares. For agencies without the required hardware, GLM-4.7-Flash should be noted as an aspirational addition pending GPU investment. For bilingual work on current hardware, Gemma 4 E4B's multilingual capability provides a starting point while hardware is evaluated.

---

## Model-Task Matching Guide

The following table maps common law enforcement analytical tasks to recommended models from this deployment. The "Primary" column is the first model to try. The "Alternative" column is a fallback if primary output quality is insufficient or hardware is unavailable.

| Task | Primary Model | Alternative | Notes |
|------|--------------|-------------|-------|
| **Document summarization** | Gemma 4 (E4B) | GPT-OSS 20B | Gemma for multimodal + recent training; GPT-OSS for structured summary formats |
| **Data extraction** (names, dates, locations) | GPT-OSS 20B | Granite 4 Micro | GPT-OSS for complex documents; Granite for RAG pipeline extraction |
| **Research synthesis** (multi-source briefings) | DeepSeek-R1 14B | Nemotron-Cascade-2 | DeepSeek for transparent reasoning; Nemotron for very long document sets (256K) |
| **Content drafting** (reports, memos, BOLOs) | Gemma 4 (E4B) | GPT-OSS 20B | Gemma for general prose; GPT-OSS for templated/structured output |
| **Analytical reasoning** (pattern analysis, logic) | DeepSeek-R1 14B | Nemotron-Cascade-2 | DeepSeek shows reasoning steps; Nemotron for most complex multi-step analysis |
| **Lightweight / fast tasks** | Phi-4 Mini | Granite 4 Micro | Phi-4 Mini for quick answers; Granite for RAG-backed fast retrieval |
| **Bilingual content** (Chinese-English) | GLM-4.7-Flash | Gemma 4 (E4B) | GLM purpose-built for Chinese-English; Gemma as hardware-compatible fallback |
| **RAG / document grounding** | Granite 4 Micro | GPT-OSS 20B | Granite designed for enterprise RAG; GPT-OSS for tool-calling pipelines |
| **Long document analysis** (>50K tokens) | Nemotron-Cascade-2 | DeepSeek-R1 14B | Nemotron's 256K context handles full case files; DeepSeek handles up to 128K |
| **Agentic / automated workflows** | GPT-OSS 20B | Nemotron-Cascade-2 | GPT-OSS designed for tool-calling; Nemotron for advanced multi-step agentic tasks |
| **Image + text analysis** | Gemma 4 (E4B) | *(none in this deployment)* | Gemma is the only multimodal model deployed; no hardware-compatible alternative |

**Recommendation:** Start with **Gemma 4 (E4B)** as the default for most tasks. Pull **DeepSeek-R1 14B** as a secondary for analytical reasoning. Use **GPT-OSS 20B** for structured pipelines. Add **Phi-4 Mini** or **Granite 4 Micro** for background/lightweight service roles.

---

## What These Models Are Bad At

Honest expectations prevent wasted effort. These limitations apply broadly across all models in this deployment.

### Complex Multi-Step Reasoning

Models at 8B-14B parameter scale struggle with problems requiring 5+ sequential logical steps. A task like "Compare the timelines from these three reports, identify contradictions, cross-reference with known suspect locations, and assess the likelihood of connection" exceeds reliable single-pass execution for most models. Break tasks into smaller sequential steps and run them separately. DeepSeek-R1 14B and Nemotron-Cascade-2 handle multi-step reasoning best, but even these models benefit from task decomposition.

### Nuanced Legal and Policy Analysis

No model in this deployment was trained on comprehensive legal corpora at the depth required for authoritative legal analysis. "Does this evidence meet the probable cause standard?" is not a reliable question for any local LLM. Use legal research tools and consult legal counsel for these determinations. The models can assist with *drafting* legal language from provided templates, but cannot reliably *interpret* statutes or case law.

### Long Document Comprehension

Despite the context windows listed above, practical quality degrades for all models at context lengths beyond their reliable range. A 200-page case file cannot simply be pasted into a prompt and reliably summarized in a single pass even for Nemotron-Cascade-2 (256K context). For long documents, chunking and sequential processing -- or a dedicated RAG pipeline -- produces more reliable results. See [Knowledge Management](08-knowledge-management.md) for RAG architecture guidance.

### Tasks Requiring Current Knowledge

All models have training data cutoffs. Events after those dates are outside the model's knowledge. The most current cutoff in this deployment is Nemotron-Cascade-2 (~June 2025) and Gemma 4 E4B (January 2025); others range from June to July 2024. For current-events-dependent analysis, always provide the relevant context within the prompt rather than assuming the model knows about recent developments.

### Mathematical Calculations

All models in this deployment are unreliable for arithmetic, statistics, and mathematical reasoning. If the task involves calculations, use a calculator or spreadsheet and paste the results into the prompt for the model to incorporate into its output. Do not rely on any model here to compute.

### The Quality Gap vs. Commercial Models

At the 3B-30B parameter range running locally, a meaningful quality gap exists compared to frontier commercial models (GPT-4, Claude, Gemini Ultra). Local models are not a replacement for commercial models in raw capability -- they are a replacement in security posture. The tradeoff is intentional: lower capability in exchange for complete data sovereignty.

| Dimension | Local Deployed Models | Commercial Frontier Models |
|-----------|----------------------|---------------------------|
| Reasoning depth | Limited to moderate (Nemotron-Cascade-2 best) | Strong |
| Factual accuracy | Moderate (requires human verification) | Higher (still requires verification) |
| Instruction following | Good to very good | Excellent |
| Long document handling | Up to 256K with quality degradation | Handles 100K+ more reliably |
| Hallucination rate | Higher | Lower (but not zero) |
| Data sovereignty | Complete | Depends on vendor agreement |
| Cost per query | Near zero (electricity) | $0.01–0.10+ per query |
| Availability | 100% (no internet required) | Requires internet + vendor uptime |

**Bottom line:** Local models handle 70–80% of routine analytical tasks adequately. The remaining 20–30% either requires larger models, different approaches (chunking, RAG), or is genuinely outside the local capability range. See [Limitations and Tradeoffs](10-limitations-tradeoffs.md) for a deeper discussion.

---

## How to Evaluate Models for Your Workflow

Model benchmarks published online do not reflect real-world performance on law enforcement-specific tasks. The only reliable evaluation method is testing with representative tasks from actual work.

### Step 1: Create a Test Set

Compile 5–10 representative tasks that reflect actual analytical work. Use non-CJI training data only. Include:

- A document that needs summarizing
- A briefing that needs drafting from provided source material
- A data extraction task (pull names, dates, and locations from a narrative)
- A reasoning task (identify patterns or contradictions across multiple inputs)
- A formatting task (generate a structured report from unstructured input)

### Step 2: Test Each Model with Identical Prompts

Run the same prompt against each candidate model. Use the prompting techniques from the [Prompting Guide](06-prompting-guide.md) to ensure prompts are well-structured.

```bash
# Pull the hardware-compatible models
ollama pull gemma4:latest
ollama pull deepseek-r1:14b
ollama pull gpt-oss:20b
ollama pull phi4-mini:latest
ollama pull granite4:latest

# Test each against the same prompt
ollama run gemma4:latest "Your test prompt here"
ollama run deepseek-r1:14b "Your test prompt here"
ollama run gpt-oss:20b "Your test prompt here"
ollama run phi4-mini:latest "Your test prompt here"
ollama run granite4:latest "Your test prompt here"
```

### Step 3: Score Outputs Against Criteria

Evaluate each output against these dimensions:

| Criterion | What to Look For |
|-----------|-----------------|
| **Accuracy** | Are facts from the source material correctly represented? |
| **Completeness** | Did the model address all parts of the prompt? |
| **Format compliance** | Does the output match the requested format? |
| **Hallucination** | Did the model add information not present in the source? |
| **Coherence** | Is the output logically structured and readable? |
| **Speed** | How long did the response take? Is it practical for daily use? |

### Step 4: Document Results

Record which model performs best for each task type. Expect results like:

- "Gemma 4 produces the best drafts for daily briefings and handles image evidence"
- "DeepSeek-R1 14B provides the most transparent reasoning for pattern analysis"
- "GPT-OSS 20B is most consistent for structured extraction and formatted output"

### Step 5: Consider Running Multiple Models

Ollama supports pulling and running multiple models on the same machine. Switching between models takes seconds.

```bash
ollama run gemma4:latest       # For drafting, summarization, and image analysis
ollama run deepseek-r1:14b     # For analytical reasoning and pattern analysis
ollama run gpt-oss:20b         # For structured output and tool-calling workflows
```

---

## Quantization Explained

Quantization reduces a model's numerical precision (from 16-bit to 4-bit or 8-bit) to decrease memory footprint and increase speed. Models in this deployment use different quantization levels as noted in each profile.

| Quantization Level | Quality Impact | VRAM Savings | When to Use |
|--------------------|---------------|--------------|-------------|
| **Q8** (8-bit) | Minimal quality loss | ~50% vs. full precision | When VRAM is available and quality matters most -- GLM-4.7-Flash uses this |
| **Q4_K_M** (4-bit, default) | Small quality loss | ~75% vs. full precision | Default for most models -- best balance of quality and resource usage |
| **Q4_K_S** (4-bit, small) | Moderate quality loss | ~78% vs. full precision | When VRAM is tight |
| **Q3_K** (3-bit) | Noticeable quality loss | ~80% vs. full precision | Not recommended for analytical work |

**Recommendation:** Use the default quantization (Q4_K_M) for all models unless a specific quantization is required for hardware fit. GLM-4.7-Flash runs at Q8_0 in this deployment because Q8 is needed to preserve quality at the 30B scale; the hardware requirement reflects this choice.

---

## Running Multiple Models

Ollama stores models on disk and loads them into VRAM on demand. Having multiple models pulled does not consume additional VRAM -- only the actively running model occupies GPU memory.

```bash
# Pull the full hardware-compatible deployment stack
ollama pull gemma4:latest
ollama pull deepseek-r1:14b
ollama pull gpt-oss:20b
ollama pull phi4-mini:latest
ollama pull granite4:latest

# Check what's available
ollama list

# Switch between models as needed
ollama run gemma4:latest "Summarize this document..."
ollama run deepseek-r1:14b "Analyze the pattern across these incidents..."
ollama run gpt-oss:20b "Extract all names, dates, and locations in JSON format..."
```

**Storage requirements:** At Q4_K_M quantization, most 3B-14B models require 2–9 GB of disk space. GPT-OSS 20B at ~13 GB and Nemotron-Cascade-2 at ~24 GB are larger. The recommended 1TB NVMe SSD from the [Hardware Guide](04-hardware-guide.md) comfortably holds the full deployment stack plus working space.

**Hardware-constrained deployments:** Nemotron-Cascade-2 (24 GB) and GLM-4.7-Flash (31 GB) require hardware beyond the standard starter build. Pull these only on machines with confirmed 24GB+ and 32GB+ VRAM respectively. The remaining five models all fit on the standard 16GB RTX 4060 Ti build.

---

## References

- Ollama Model Library -- [ollama.com/library](https://ollama.com/library)
- Google Gemma 4 -- [ai.google.dev/gemma](https://ai.google.dev/gemma)
- DeepSeek-R1 -- [github.com/deepseek-ai](https://github.com/deepseek-ai)
- OpenAI GPT-OSS -- [openai.com](https://openai.com)
- Microsoft Phi-4 -- [azure.microsoft.com/products/phi-4](https://azure.microsoft.com/en-us/products/phi-4)
- IBM Granite 4 -- [ibm.com/granite](https://www.ibm.com/granite)
- NVIDIA Nemotron-Cascade-2 -- [research.nvidia.com](https://research.nvidia.com/labs/nemotron)
- ZhipuAI GLM-4.7-Flash -- [github.com/THUDM](https://github.com/THUDM)
- CJIS Security Policy v6.0 -- [FBI CJIS Resource Center](https://www.fbi.gov/services/cjis)

---

*This document is part of the [NFCA Open Source LLM](../README.md) companion resource. For prompting techniques to get the most from these models, see the [Prompting Guide](06-prompting-guide.md). For hardware requirements, see the [Hardware Guide](04-hardware-guide.md). This is an educational resource, not official guidance. Consult your agency's CJIS Systems Officer (CSO) for compliance decisions.*
