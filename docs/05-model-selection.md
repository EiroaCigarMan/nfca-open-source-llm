# Model Selection for Law Enforcement Workflows

> **Summary:** Not all models are equal, and not all tasks require the same model. This guide covers the full landscape of open-source models available through Ollama -- with exact pull commands, honest capability assessments, VRAM requirements, and task-matching recommendations. The goal is to help practitioners choose the right tool for the right job, avoid wasting time on models that do not fit their hardware, and set realistic expectations about what these models can and cannot do.

---

## Table of Contents

- [How to Read This Guide](#how-to-read-this-guide)
- [Recommended Models -- Full Landscape](#recommended-models----full-landscape)
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
- **Best use cases** -- what the model does well
- **Key strengths** -- where the model outperforms peers
- **Key weaknesses** -- where the model falls short
- **Honest assessment** -- practical observations from real-world testing

All VRAM estimates assume default (Q4_K_M) quantization unless otherwise noted. Actual VRAM usage varies by context length and concurrent sessions.

---

## Recommended Models -- Full Landscape

### Quick Reference Table

| Model | Parameters | VRAM (approx.) | Fits 16GB GPU? | Primary Use Case |
|-------|-----------|----------------|----------------|------------------|
| [Llama 3.1 8B-Instruct](#llama-31-8b-instruct) | 8B | ~6 GB | Yes | General-purpose workhorse |
| [Llama 3.3 70B (quantized)](#llama-33-70b-quantized) | 70B | ~40-48 GB | No | Highest quality local reasoning |
| [Mistral 7B-Instruct v0.3](#mistral-7b-instruct-v03) | 7B | ~5 GB | Yes | Efficient general tasks |
| [Mixtral 8x7B](#mixtral-8x7b) | 46.7B (12.9B active) | ~26 GB | No | Expert-level MoE architecture |
| [Qwen 2.5 / Qwen3-8B](#qwen-25--qwen3-8b) | 8B | ~6 GB | Yes | Multilingual + reasoning |
| [Phi-3 Mini 3.8B](#phi-3-mini-38b) | 3.8B | ~3 GB | Yes | Fast lightweight tasks |
| [Phi-3 Medium 14B](#phi-3-medium-14b) | 14B | ~9 GB | Yes | Mid-range capable |
| [DeepSeek-R1-Distill 8B](#deepseek-r1-distill-8b) | 8B | ~6 GB | Yes | Chain-of-thought reasoning |
| [Gemma 2 9B](#gemma-2-9b) | 9B | ~7 GB | Yes | Instruction following |
| [Command-R 35B](#command-r-35b) | 35B | ~21 GB | No | RAG and tool use |
| [CodeGemma 7B](#codegemma-7b) | 7B | ~5 GB | Yes | Code and scripting tasks |

---

## Model Profiles

### Llama 3.1 8B-Instruct

```bash
ollama pull llama3.1:8b
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | Meta |
| **Parameters** | 8 billion |
| **VRAM Required** | ~6 GB |
| **Fits 16GB GPU** | Yes |
| **Context Window** | 128K tokens (but quality degrades significantly past ~8K) |
| **License** | Llama 3.1 Community License (permissive with usage thresholds) |

**Best Use Cases:**
- Document summarization and extraction
- Report and memo drafting
- General analytical writing
- Template-based content generation (briefings, BOLOs)

**Key Strengths:**
- Strong all-around performer at the 8B scale
- Excellent instruction following -- does what the prompt asks
- Large training dataset produces coherent, well-structured outputs
- Broad community support and extensive documentation
- 128K token context window (theoretical maximum)

**Key Weaknesses:**
- Context quality degrades noticeably past ~8K tokens in practice
- Struggles with complex multi-step reasoning chains
- Mathematical and numerical tasks are unreliable
- Not the strongest at structured data extraction compared to Qwen

**Honest Assessment:** This is the default recommendation for most law enforcement analytical workflows. It handles summarization, drafting, and template-based generation well. Expect to refine prompts for 1-2 weeks before outputs are consistently useful. For most agencies starting with local LLMs, this is the right first model to deploy.

---

### Llama 3.3 70B (quantized)

```bash
ollama pull llama3.3:70b-instruct-q4_K_M
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | Meta |
| **Parameters** | 70 billion |
| **VRAM Required** | ~40-48 GB (quantized Q4_K_M) |
| **Fits 16GB GPU** | No -- requires dual high-end GPUs or a single 48GB+ card (e.g., RTX A6000, dual RTX 3090) |
| **Context Window** | 128K tokens |
| **License** | Llama 3.3 Community License |

**Best Use Cases:**
- Complex analytical reasoning that smaller models cannot handle
- Nuanced document analysis requiring deeper comprehension
- Multi-document synthesis with higher fidelity
- Tasks where output quality justifies the hardware investment

**Key Strengths:**
- Dramatically better reasoning than any 7-13B model
- Stronger factual grounding and fewer hallucinations
- Better at following complex, multi-part instructions
- Approaches commercial model quality for many tasks

**Key Weaknesses:**
- Requires $2,000+ in GPU hardware (48GB+ VRAM)
- Significantly slower inference than 8B models
- Power consumption is substantially higher
- Not practical for the recommended $1,000-1,500 workstation build

**Honest Assessment:** This is the model to aspire to, not the model to start with. For agencies that have proven the value of local LLMs with an 8B model and want to scale up capability, 70B represents a significant quality improvement. But the hardware cost is 2-3x the starter build. Most agencies should start with Llama 3.1 8B and evaluate whether the quality gap justifies the upgrade. See the [Hardware Guide](04-hardware-guide.md) for GPU options that support 70B inference.

---

### Mistral 7B-Instruct v0.3

```bash
ollama pull mistral:7b
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | Mistral AI (France) |
| **Parameters** | 7 billion |
| **VRAM Required** | ~5 GB |
| **Fits 16GB GPU** | Yes |
| **Context Window** | 32K tokens |
| **License** | Apache 2.0 (fully permissive) |

**Best Use Cases:**
- Research synthesis from multiple source documents
- General question-answering and information retrieval
- Administrative document drafting
- Lighter workloads where speed matters more than depth

**Key Strengths:**
- Fast inference -- noticeably quicker than Llama 3.1 8B for equivalent tasks
- Apache 2.0 license means zero restrictions on government use
- Lower VRAM footprint leaves headroom for longer contexts
- Good at following structured output formats

**Key Weaknesses:**
- Slightly less capable than Llama 3.1 8B for complex instructions
- Smaller training dataset means less broad knowledge
- Not as strong for creative or analytical writing
- 32K context window is shorter than Llama's 128K (though practical limits are similar)

**Honest Assessment:** A solid alternative to Llama 3.1 8B, especially when speed is prioritized. The Apache 2.0 license eliminates any licensing questions for government deployment. For agencies that need fast turnaround on simpler tasks -- quick summaries, straightforward Q&A, administrative drafts -- Mistral 7B is often the better choice. For deeper analytical work, Llama 3.1 8B has the edge.

---

### Mixtral 8x7B

```bash
ollama pull mixtral:8x7b
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | Mistral AI |
| **Parameters** | 46.7B total (12.9B active per inference) |
| **VRAM Required** | ~26 GB |
| **Fits 16GB GPU** | No -- requires 32GB+ VRAM (e.g., RTX 4090 24GB may work with offloading, or dual-GPU setup) |
| **Context Window** | 32K tokens |
| **License** | Apache 2.0 |

**Best Use Cases:**
- Tasks where 7-8B models fall short but 70B is impractical
- Multi-topic analysis requiring broader knowledge
- Scenarios needing better reasoning without extreme hardware costs
- Research synthesis across diverse subject areas

**Key Strengths:**
- Mixture of Experts (MoE) architecture -- only activates a subset of parameters per query, providing 47B-level knowledge with 13B-level speed
- Significantly better reasoning than any single 7-8B model
- Handles multi-part instructions more reliably
- Apache 2.0 license

**Key Weaknesses:**
- Requires 26GB+ VRAM -- does not fit the recommended starter hardware
- MoE architecture means all parameters must be loaded into VRAM even though only a subset activates
- Slower to load initially (larger model file)
- Not as well-optimized for instruction following as Llama 3.1 8B

**Honest Assessment:** Mixtral occupies an interesting middle ground. Its MoE architecture delivers better quality than single 7-8B models while being faster than a full 47B parameter model. The catch is the VRAM requirement -- agencies need at least an RTX 4090 (24GB) or better. Consider this as a "Phase 2" upgrade after proving value with an 8B model.

---

### Qwen 2.5 / Qwen3-8B

```bash
ollama pull qwen3:8b
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | Alibaba Cloud (China) |
| **Parameters** | 8 billion |
| **VRAM Required** | ~6 GB |
| **Fits 16GB GPU** | Yes |
| **Context Window** | 32K tokens (Qwen3), 128K tokens (Qwen 2.5) |
| **License** | Apache 2.0 |

**Best Use Cases:**
- Multilingual content analysis (strongest non-English support in this class)
- Structured data extraction and formatting
- Reasoning tasks requiring step-by-step logic
- Tasks involving code, tables, or structured output

**Key Strengths:**
- Best-in-class multilingual capability at the 8B scale -- particularly strong for Spanish, Chinese, Arabic, and other languages common in multilingual communities
- Strong structured output -- generates clean JSON, tables, and formatted results
- Competitive reasoning ability, especially for step-by-step tasks
- Apache 2.0 license

**Key Weaknesses:**
- Developed by a Chinese company -- some agencies may have procurement or policy concerns about model provenance (note: the model weights are open-source and auditable, and all inference runs locally with no data transmission)
- Less community documentation compared to Llama/Mistral in English-language forums
- Newer model family with a shorter track record

**Honest Assessment:** Qwen3-8B is arguably the most capable 8B model available as of early 2025. Its multilingual strength is a genuine differentiator for agencies serving diverse communities. The provenance concern is worth noting: while the model runs entirely locally and no data is transmitted, some agencies may have policies about software from specific countries. The model weights are open-source and can be independently audited. Evaluate based on your agency's specific policies.

---

### Phi-3 Mini 3.8B

```bash
ollama pull phi3:mini
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | Microsoft |
| **Parameters** | 3.8 billion |
| **VRAM Required** | ~3 GB |
| **Fits 16GB GPU** | Yes (with significant headroom) |
| **Context Window** | 128K tokens |
| **License** | MIT License (fully permissive) |

**Best Use Cases:**
- Quick summaries of short documents
- Rapid question-answering for simple queries
- Environments with very limited GPU resources (laptops, older hardware)
- First-pass drafts that will be heavily edited

**Key Strengths:**
- Extremely fast inference -- near-instant responses for short prompts
- Minimal resource usage allows running alongside other applications
- MIT license -- maximum legal clarity for government use
- Runs adequately even on integrated GPUs or CPU-only configurations
- 128K context window despite small size

**Key Weaknesses:**
- Noticeably less capable than 7-8B models for complex tasks
- More prone to hallucination than larger models
- Struggles with nuanced instructions or multi-step prompts
- Output quality drops significantly for longer, more complex requests

**Honest Assessment:** Phi-3 Mini is the "good enough for a quick answer" model. Deployment on a laptop or shared workstation where a dedicated GPU is not available is the primary use case. Do not expect the output quality of Llama 3.1 8B or Qwen3-8B. For agencies testing whether local LLMs provide value before investing in GPU hardware, Phi-3 Mini is a low-risk starting point.

---

### Phi-3 Medium 14B

```bash
ollama pull phi3:medium
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | Microsoft |
| **Parameters** | 14 billion |
| **VRAM Required** | ~9 GB |
| **Fits 16GB GPU** | Yes |
| **Context Window** | 128K tokens |
| **License** | MIT License |

**Best Use Cases:**
- Longer document analysis where Phi-3 Mini falls short
- Analytical writing that requires more coherence and depth
- Tasks where 7-8B models are borderline and a step up is needed without going to 70B

**Key Strengths:**
- Meaningfully more capable than 7-8B models for reasoning tasks
- Still fits on a 16GB GPU with room to spare
- Strong performance-per-parameter ratio (Microsoft's training approach emphasizes data quality)
- MIT license

**Key Weaknesses:**
- Slower inference than Phi-3 Mini or 7-8B models
- Less community support and fewer community-tuned variants compared to Llama
- 9 GB VRAM usage limits the room for concurrent operations
- Not as strong as Llama 3.1 8B for general instruction following in some benchmarks

**Honest Assessment:** Phi-3 Medium sits in a useful middle ground between 7-8B models and the jump to 70B. If testing shows that Llama 3.1 8B or Qwen3-8B are "almost good enough" for a particular task, Phi-3 Medium may close the gap without requiring new hardware. It is worth evaluating alongside the 8B models during initial testing.

---

### DeepSeek-R1-Distill 8B

```bash
ollama pull deepseek-r1:8b
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | DeepSeek (China) |
| **Parameters** | 8 billion |
| **VRAM Required** | ~6 GB |
| **Fits 16GB GPU** | Yes |
| **Context Window** | 64K tokens |
| **License** | MIT License |

**Best Use Cases:**
- Analytical reasoning that requires step-by-step logic
- Pattern analysis where the model needs to "show its work"
- Tasks where understanding *how* the model reached a conclusion matters
- Complex prompts requiring multi-step planning

**Key Strengths:**
- Trained specifically for chain-of-thought reasoning -- outputs its thinking process before the final answer
- Strong at breaking complex problems into logical steps
- Distilled from a much larger model (DeepSeek-R1 671B), retaining reasoning capabilities at 8B scale
- MIT license

**Key Weaknesses:**
- Reasoning trace output increases token usage and response time
- Developed by a Chinese company -- same provenance considerations as Qwen (see [Qwen profile](#qwen-25--qwen3-8b) for discussion)
- Less polished for simple generation tasks (summarization, drafting) where chain-of-thought reasoning is unnecessary overhead
- Newer model with less real-world deployment data

**Honest Assessment:** DeepSeek-R1-Distill 8B is the best reasoning model at the 8B scale. For analytical tasks -- pattern analysis, evidence synthesis, timeline reconstruction -- its chain-of-thought approach produces more transparent and verifiable outputs. The reasoning trace also makes it easier to spot where the model went wrong. The tradeoff is speed: the thinking process adds latency and token usage. Use this model for analytical depth; use Llama 3.1 8B or Mistral 7B for straightforward generation tasks.

---

### Gemma 2 9B

```bash
ollama pull gemma2:9b
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | Google DeepMind |
| **Parameters** | 9 billion |
| **VRAM Required** | ~7 GB |
| **Fits 16GB GPU** | Yes |
| **Context Window** | 8K tokens |
| **License** | Gemma Terms of Use (permissive, with restrictions on generating harmful content) |

**Best Use Cases:**
- Instruction following where the prompt is well-structured
- Short document summarization (within the 8K context window)
- Question-answering over provided context
- Tasks where adherence to output format is critical

**Key Strengths:**
- Strong instruction following -- closely adheres to prompt specifications
- Well-calibrated safety filters (may be desirable or undesirable depending on use case)
- Backed by Google's research infrastructure
- Competitive with Llama 3.1 8B on many benchmarks

**Key Weaknesses:**
- **8K token context window is a significant limitation** -- cannot process longer documents without chunking
- Safety filters can be overly aggressive, refusing legitimate analytical queries
- License is more restrictive than Apache 2.0 or MIT
- Smaller community of fine-tuned variants compared to Llama ecosystem

**Honest Assessment:** Gemma 2 9B performs well on tasks that fit within its 8K context window, but that context limitation is a real constraint for law enforcement analytical work. Case files, intelligence reports, and multi-source briefings frequently exceed 8K tokens. If most tasks involve short documents or well-structured prompts, Gemma 2 is competitive. For longer documents, Llama 3.1 8B or Qwen3-8B are better choices. The safety filters may also refuse some legitimate queries involving violence, criminal activity, or weapons -- topics that are routine in law enforcement analysis.

---

### Command-R 35B

```bash
ollama pull command-r:35b
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | Cohere |
| **Parameters** | 35 billion |
| **VRAM Required** | ~21 GB |
| **Fits 16GB GPU** | No -- requires 24GB+ VRAM (e.g., RTX 4090 or comparable) |
| **Context Window** | 128K tokens |
| **License** | CC-BY-NC-4.0 (non-commercial; government use may qualify but requires legal review) |

**Best Use Cases:**
- Retrieval-Augmented Generation (RAG) workflows -- designed specifically for working with retrieved documents
- Multi-document synthesis where the model must ground answers in provided sources
- Tool use and structured function calling
- Analytical tasks requiring grounded, citation-like responses

**Key Strengths:**
- **Purpose-built for RAG** -- significantly better than general models at working with retrieved context
- Strong citation/attribution behavior -- tends to reference specific passages from provided documents
- 128K token context window supports large document sets
- Excellent at following tool-use patterns for integration with other systems

**Key Weaknesses:**
- Requires 24GB+ VRAM -- does not fit the starter hardware build
- Non-commercial license may require legal review for government use (CC-BY-NC typically does not restrict government use, but consult your legal team)
- Slower inference at 35B scale
- Less community documentation than Llama ecosystem

**Honest Assessment:** Command-R 35B is the model to evaluate if RAG workflows are the primary use case (see [Knowledge Management](08-knowledge-management.md) for RAG architecture). Its ability to ground responses in provided documents reduces hallucination for document-based analytical tasks. However, the hardware requirement and licensing considerations place it in the "Phase 2" category for most agencies. Start with Llama 3.1 8B for RAG tasks; upgrade to Command-R when the value is proven and hardware supports it.

---

### CodeGemma 7B

```bash
ollama pull codegemma:7b
```

| Attribute | Detail |
|-----------|--------|
| **Developer** | Google DeepMind |
| **Parameters** | 7 billion |
| **VRAM Required** | ~5 GB |
| **Fits 16GB GPU** | Yes |
| **Context Window** | 8K tokens |
| **License** | Gemma Terms of Use |

**Best Use Cases:**
- Writing scripts for data processing or automation
- Generating SQL queries against databases
- Code review and debugging
- Converting manual processes to automated scripts

**Key Strengths:**
- Optimized for code generation across multiple programming languages
- Better at SQL query generation than general-purpose models
- Can help non-programmers automate repetitive data tasks
- Understands code structure, syntax, and common patterns

**Key Weaknesses:**
- **Not a general-purpose model** -- significantly worse than Llama/Mistral/Qwen for non-code tasks
- 8K context window limits the size of code files it can process
- Should not be used for document analysis, summarization, or analytical writing
- Generated code must be reviewed and tested before use

**Honest Assessment:** CodeGemma is a specialist. Deploy it alongside a general-purpose model when scripting or data automation is part of the workflow. For example, an analyst who needs to write a Python script to parse CSV data or generate SQL queries against a case management database will get better results from CodeGemma than from Llama 3.1 8B. But every piece of generated code must be reviewed and tested -- code hallucination is real and can produce scripts that appear correct but contain logical errors.

---

## Model-Task Matching Guide

The following table maps common law enforcement analytical tasks to recommended models. The "Primary" column is the first model to try. The "Alternative" column is a fallback if the primary model's output quality is insufficient.

| Task | Primary Model | Alternative | Notes |
|------|--------------|-------------|-------|
| **Document summarization** | Llama 3.1 8B | Qwen3-8B | Both handle summarization well; Qwen for multilingual content |
| **Data extraction** (names, dates, locations) | Qwen3-8B | Llama 3.1 8B | Qwen excels at structured extraction |
| **Research synthesis** (multi-source briefings) | Mistral 7B | Qwen3-8B | Mistral is faster; Qwen has better reasoning |
| **Content drafting** (reports, memos, BOLOs) | Llama 3.1 8B | Mistral 7B | Llama produces more polished prose |
| **Analytical reasoning** (pattern analysis, logic) | DeepSeek-R1-Distill 8B | Phi-3 Medium 14B | DeepSeek shows reasoning steps; Phi-3 Medium for more subtle analysis |
| **Lightweight / fast tasks** | Phi-3 Mini 3.8B | Mistral 7B | Phi-3 Mini for quick answers; Mistral for slightly better quality |
| **Code and scripting** | CodeGemma 7B | Qwen3-8B | CodeGemma is specialist; Qwen is a capable generalist for code |
| **Multilingual content** | Qwen3-8B | Llama 3.1 8B | Qwen is significantly better for non-English text |
| **RAG / document grounding** | Command-R 35B | Llama 3.1 8B | Command-R requires 24GB+ VRAM; Llama is the practical starter |
| **Complex reasoning** (multi-step analysis) | Llama 3.3 70B (quantized) | DeepSeek-R1-Distill 8B | 70B for highest quality; DeepSeek-R1 for best results on 16GB hardware |

**Recommendation:** Start with **Llama 3.1 8B** as the default model for most tasks. Pull **Qwen3-8B** and **DeepSeek-R1-Distill 8B** as secondary models for multilingual and reasoning tasks respectively. Add specialists (CodeGemma, Command-R) only when specific needs emerge.

---

## What These Models Are Bad At

Honest expectations prevent wasted effort. These limitations apply to **all models in the 7B-13B parameter range** running on commodity hardware.

### Complex Multi-Step Reasoning

Models at this scale struggle with problems requiring 5+ sequential logical steps. A task like "Compare the timelines from these three reports, identify contradictions, cross-reference with known suspect locations, and assess the likelihood of connection" exceeds what most 7-8B models can reliably execute in a single pass. Breaking the task into smaller steps and running them sequentially produces better results.

### Nuanced Legal and Policy Analysis

These models were not trained on comprehensive legal corpora and cannot reliably interpret statutes, case law, or regulatory requirements. "Does this evidence meet the probable cause standard under the Fourth Amendment?" is not a question for a 7B model. Use legal research tools and consult legal counsel for these tasks.

### Long Document Comprehension

Despite advertised context windows of 32K-128K tokens, practical quality degrades significantly for most 7-8B models beyond ~4,000-8,000 tokens. A 200-page case file cannot simply be pasted into a prompt and summarized. Effective use requires chunking documents and processing them in segments. See [Knowledge Management](08-knowledge-management.md) for RAG-based approaches to long documents.

### Tasks Requiring Broad World Knowledge

These models have knowledge cutoff dates and limited training data compared to commercial models. Asking for current events, recent legal developments, or niche domain knowledge will frequently produce hallucinated or outdated answers. Always provide the relevant context within the prompt rather than assuming the model "knows" something.

### Mathematical Calculations

All models at this scale are unreliable for arithmetic, statistics, and mathematical reasoning. If the task involves calculations, use a calculator or spreadsheet and paste the results into the prompt for the model to incorporate into its output. Do not rely on the model to compute.

### The Quality Gap vs. Commercial Models

At the 7B-13B parameter range, a meaningful quality gap exists compared to GPT-4, Claude, or Gemini Pro. These commercial models have hundreds of billions of parameters, access to more training data, and undergo extensive alignment fine-tuning. Local 7-8B models are not a replacement for commercial models in capability -- they are a replacement in security posture. The tradeoff is intentional: lower capability in exchange for complete data sovereignty.

| Dimension | Local 7-8B Model | Commercial Model (GPT-4/Claude) |
|-----------|-------------------|----------------------------------|
| Reasoning depth | Limited | Strong |
| Factual accuracy | Moderate (requires verification) | Higher (still requires verification) |
| Instruction following | Good | Excellent |
| Long document handling | Degrades past ~8K tokens | Handles 100K+ tokens more reliably |
| Hallucination rate | Higher | Lower (but not zero) |
| Data sovereignty | Complete | Depends on vendor agreement |
| Cost per query | Near zero (electricity) | $0.01-0.10+ per query |
| Availability | 100% (no internet required) | Requires internet + vendor uptime |

**Bottom line:** Local models handle 70-80% of routine analytical tasks adequately. The remaining 20-30% either requires larger models, different approaches (like chunking documents), or is genuinely outside the capability range. See [Limitations and Tradeoffs](10-limitations-tradeoffs.md) for a deeper discussion.

---

## How to Evaluate Models for Your Workflow

Model benchmarks published online do not reflect real-world performance on law enforcement-specific tasks. The only reliable evaluation method is testing with representative tasks from actual work.

### Step 1: Create a Test Set

Compile 5-10 representative tasks that reflect the actual analytical work being performed. These should include:

- A document that needs summarizing (use non-CJI training data)
- A briefing that needs drafting from provided source material
- A data extraction task (pull names, dates, and locations from a narrative)
- A reasoning task (identify patterns or contradictions across multiple inputs)
- A formatting task (generate a structured report from unstructured input)

### Step 2: Test Each Model with Identical Prompts

Run the same prompt against each candidate model. Use the prompting techniques from the [Prompting Guide](06-prompting-guide.md) to ensure prompts are well-structured.

```bash
# Pull candidates
ollama pull llama3.1:8b
ollama pull qwen3:8b
ollama pull deepseek-r1:8b
ollama pull mistral:7b

# Test each
ollama run llama3.1:8b "Your test prompt here"
ollama run qwen3:8b "Your test prompt here"
ollama run deepseek-r1:8b "Your test prompt here"
ollama run mistral:7b "Your test prompt here"
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

Record which model performs best for which task type. Expect results like:

- "Llama 3.1 8B produces the best drafts for daily briefings"
- "Qwen3-8B is more reliable for extracting structured data from reports"
- "DeepSeek-R1-Distill 8B provides the most transparent reasoning for pattern analysis"

### Step 5: Consider Running Multiple Models

Ollama supports pulling and running multiple models on the same machine. Keep 2-3 models available and use the best model for each task type. Switching between models takes seconds:

```bash
ollama run llama3.1:8b    # For drafting and summarization
ollama run deepseek-r1:8b  # For analytical reasoning
ollama run qwen3:8b        # For multilingual content
```

---

## Quantization Explained

Quantization is the process of reducing a model's precision (from 16-bit to 4-bit, for example) to decrease its memory footprint and increase speed. All models listed above use default quantization (typically Q4_K_M in Ollama).

| Quantization Level | Quality Impact | VRAM Savings | When to Use |
|---------------------|---------------|--------------|-------------|
| **Q8** (8-bit) | Minimal quality loss | ~50% vs. full precision | When VRAM is available and quality matters |
| **Q4_K_M** (4-bit, default) | Small quality loss | ~75% vs. full precision | Default -- best balance of quality and resource usage |
| **Q4_K_S** (4-bit, small) | Moderate quality loss | ~78% vs. full precision | When VRAM is tight |
| **Q3_K** (3-bit) | Noticeable quality loss | ~80% vs. full precision | Not recommended for analytical work |

**Recommendation:** Use the default quantization (Q4_K_M) for all 7-13B models. Only consider higher quantization (Q8) if testing reveals quality issues and VRAM permits. Avoid Q3 and below -- the quality degradation is noticeable for analytical tasks.

To pull a specific quantization:

```bash
# Default (Q4_K_M) -- recommended
ollama pull llama3.1:8b

# Explicit 8-bit for higher quality (uses more VRAM)
ollama pull llama3.1:8b-instruct-q8_0
```

---

## Running Multiple Models

Ollama stores models on disk and loads them into VRAM on demand. Having multiple models pulled does not consume additional VRAM -- only the actively running model occupies GPU memory.

```bash
# Pull multiple models (stored on disk)
ollama pull llama3.1:8b
ollama pull qwen3:8b
ollama pull deepseek-r1:8b
ollama pull phi3:mini

# Check what's available
ollama list

# Switch between models as needed
ollama run llama3.1:8b "Summarize this report..."
ollama run deepseek-r1:8b "Analyze the pattern between..."
```

**Storage requirements:** Each 7-8B model at Q4_K_M quantization requires approximately 4-5 GB of disk space. The recommended 1TB NVMe SSD from the [Hardware Guide](04-hardware-guide.md) can comfortably hold 50+ models.

---

## References

- Ollama Model Library -- [ollama.com/library](https://ollama.com/library)
- Meta Llama 3.1 -- [llama.meta.com](https://llama.meta.com)
- Mistral AI Models -- [mistral.ai](https://mistral.ai)
- Alibaba Qwen -- [github.com/QwenLM](https://github.com/QwenLM)
- Microsoft Phi-3 -- [azure.microsoft.com/en-us/products/phi-3](https://azure.microsoft.com/en-us/products/phi-3)
- DeepSeek -- [github.com/deepseek-ai](https://github.com/deepseek-ai)
- Google Gemma -- [ai.google.dev/gemma](https://ai.google.dev/gemma)
- Cohere Command-R -- [cohere.com/command](https://cohere.com/command)
- CJIS Security Policy v6.0 -- [FBI CJIS Resource Center](https://www.fbi.gov/services/cjis)

---

*This document is part of the [NFCA Open Source LLM](../README.md) companion resource. For prompting techniques to get the most from these models, see the [Prompting Guide](06-prompting-guide.md). For hardware requirements, see the [Hardware Guide](04-hardware-guide.md). This is an educational resource, not official guidance. Consult your agency's CJIS Systems Officer (CSO) for compliance decisions.*
