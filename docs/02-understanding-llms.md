# Understanding LLMs -- How They Actually Work

> **Summary:** Large Language Models are probabilistic text generators, not knowledge databases. They predict likely word sequences based on training data -- they do not "know" things or retrieve facts from a database. Feeding relevant data into a prompt *significantly reduces* hallucination but does not eliminate it. Temperature=0 makes outputs more *consistent*, not more *truthful*. Every output must be reviewed by a qualified human before operational use. This document corrects common misconceptions and sets realistic expectations.

---

## Table of Contents

- [What LLMs Actually Are](#what-llms-actually-are)
- [Hallucination -- The Critical Reality](#hallucination----the-critical-reality)
- [Temperature Settings -- What They Actually Control](#temperature-settings----what-they-actually-control)
- [Common Questions and Honest Answers](#common-questions-and-honest-answers)
- [Setting Realistic Expectations](#setting-realistic-expectations)
- [References](#references)

---

## What LLMs Actually Are

A Large Language Model is a statistical pattern-matching system trained on massive amounts of text data. Understanding what that means -- and what it does not mean -- is essential before deploying one in any professional environment.

### How They Work (Simplified)

An LLM generates text one word (technically, one "token") at a time. For each token, the model calculates the probability of every possible next token and selects one. This process repeats until the response is complete.

```
Input:  "The suspect was last seen heading..."

Model's internal process (simplified):
  "north"     → 18% probability
  "south"     → 15% probability
  "toward"    → 12% probability
  "east"      → 10% probability
  "westbound" →  8% probability
  ...thousands more options with decreasing probability

Output: "north" (selected as highest probability)
```

This is fundamentally different from a database query. A database retrieves a stored record. An LLM generates a statistically probable continuation of the input text.

### What They Are NOT

| Common Misconception | Reality |
|----------------------|---------|
| "It's an AI that knows things" | It predicts probable text completions based on patterns in training data |
| "It retrieves facts from its training data" | It generates text that *resembles* patterns from training data -- it has no retrieval mechanism |
| "If I feed it data, it will only use that data" | It blends provided context with patterns from training data -- it cannot fully isolate one from the other |
| "It understands what I'm asking" | It processes statistical relationships between tokens -- the question of "understanding" remains an open research debate |
| "It's like a search engine" | Search engines retrieve existing documents; LLMs generate new text that may or may not be accurate |

### Training Data and Cutoff Dates

LLMs are trained on text data up to a specific date. They have no awareness of events, laws, policies, or case developments after that cutoff. This means:

- A model trained through January 2024 has no knowledge of legal developments from February 2024 onward
- Policy changes, new case law, and updated procedures after the training cutoff are invisible to the model
- The model will not tell you its information is outdated -- it will generate responses as if its training data is current

This is one of the strongest arguments for providing relevant context documents directly in the prompt rather than relying on the model's internal knowledge. For more on how to feed context into prompts effectively, see the [Prompting Guide](06-prompting-guide.md).

---

## Hallucination -- The Critical Reality

"Hallucination" is the term for when an LLM generates text that is plausible-sounding but factually incorrect. This is not a bug that will be fixed in a future update -- it is a fundamental characteristic of how probabilistic text generation works.

### The Corrected Claim

A common claim is that feeding relevant data to a model "eliminates hallucination risks." The accurate statement is:

> **Providing relevant context in the prompt *significantly reduces* hallucination but does NOT eliminate it.**

Even with perfect, complete context provided in the prompt, a model can still:

- **Misattribute information** -- Assign a statement to the wrong person or document
- **Misquote** -- Generate a close paraphrase and present it as a direct quote
- **Incorrectly synthesize** -- Combine facts from two separate sections in a way that creates a false conclusion
- **Invent plausible details** -- Fill gaps in the provided context with statistically likely but fabricated specifics
- **Hallucinate structure** -- Generate realistic-looking case numbers, dates, or identifiers that do not exist in the source material

### Model Size Matters

Hallucination frequency correlates with model size. Smaller models hallucinate more frequently and more confidently than larger models.

| Model Size | Approximate Hallucination Risk | Practical Implication |
|------------|-------------------------------|----------------------|
| 3B-7B parameters | Higher frequency | Requires careful review of every output; best used for simple, structured tasks |
| 8B-13B parameters | Moderate frequency | Suitable for drafting and analysis with thorough human review |
| 30B-70B parameters | Lower frequency | More reliable for complex tasks, but still requires review |
| 70B+ parameters | Lowest frequency | Closest to commercial model quality, but hallucination is never zero |

Models in the 7B-8B range -- which are what a standard workstation with 16GB VRAM can run (see [Hardware Guide](04-hardware-guide.md)) -- fall in the moderate-risk category. They are useful tools, but the human reviewer is not optional.

### Real-World Hallucination Examples

These examples illustrate the types of errors that occur in law enforcement analytical contexts:

**Example 1: Misattribution**
- **Source material:** Report A states the vehicle was blue. Report B states the vehicle was black.
- **Model output:** "According to Report A, the vehicle was described as black."
- **What happened:** The model correctly identified both vehicle descriptions but attributed Report B's description to Report A.

**Example 2: Fabricated Specificity**
- **Source material:** "The incident occurred in the early morning hours."
- **Model output:** "The incident occurred at approximately 0315 hours."
- **What happened:** The model converted a vague time reference into a specific time that appears authoritative but was never in the source.

**Example 3: Incorrect Synthesis**
- **Source material:** Suspect A was seen at Location X on Monday. Suspect B was seen at Location X on Tuesday.
- **Model output:** "Suspects A and B were observed together at Location X."
- **What happened:** The model linked two true facts (same location) into a false conclusion (seen together) by omitting the different dates.

### The Bottom Line on Hallucination

Every output from a local LLM must be reviewed by a qualified human before operational use. This is not a limitation that can be configured away. It is a fundamental characteristic of the technology.

The value of a local LLM is that it produces a first draft in minutes instead of hours. The human reviewer then validates, corrects, and approves. The total workflow -- AI draft plus human review -- is still significantly faster than manual drafting from scratch. But the human review step is non-negotiable.

---

## Temperature Settings -- What They Actually Control

Temperature is a parameter that controls how much randomness the model uses when selecting the next token. It is frequently misunderstood.

### The Corrected Claim

A common claim is that setting temperature to 0 produces "deterministic, factual" responses. The accurate statement is:

> **Temperature=0 makes outputs more *consistent and repeatable*, not more *truthful*. The model has no "factual mode."**

### How Temperature Works

| Temperature | Behavior | What This Means |
|-------------|----------|-----------------|
| 0.0 | Model selects the single highest-probability token every time | Most consistent output; running the same prompt twice tends to produce the same response |
| 0.1-0.3 | Very slight randomness | Nearly identical to 0 for most tasks; occasionally varies word choice |
| 0.4-0.7 | Moderate randomness | More varied phrasing; useful for creative or drafting tasks |
| 0.8-1.0 | High randomness | Wide variety of outputs; useful for brainstorming but unreliable for analysis |
| >1.0 | Very high randomness | Increasingly incoherent; not recommended for professional use |

### Why Temperature=0 Is Not a "Truth Mode"

Temperature controls *which* of the probable next tokens the model selects. It does not change *what* the model considers probable. If the model's highest-probability completion of a sentence is factually incorrect, temperature=0 will consistently produce that incorrect output.

Consider:
- At temperature=0.7, a model might hallucinate a different incorrect detail each time
- At temperature=0, the model hallucinate the *same* incorrect detail every time

Consistency is not accuracy. A model that reliably produces the same wrong answer is not more trustworthy than one that produces different wrong answers -- it is simply more predictable.

### Implementation Details

Even at temperature=0, some LLM implementations introduce minor randomness through:

- **Top-k sampling** -- Some runtimes use a small top-k window even at temp=0
- **Floating-point non-determinism** -- GPU computation can produce slightly different results across runs due to parallel processing order
- **Batching effects** -- The same prompt may produce slightly different outputs depending on what other requests the system is processing

For most practical purposes, temperature=0 produces highly consistent output. But it should not be treated as a guarantee of determinism or correctness.

### Practical Recommendation

For law enforcement analytical work:

| Use Case | Recommended Temperature | Rationale |
|----------|------------------------|-----------|
| Summarization | 0.0 - 0.1 | Consistency matters; want the same summary each time |
| Report drafting | 0.2 - 0.4 | Slight variation produces more natural-sounding prose |
| Brainstorming / hypotheses | 0.5 - 0.7 | Want diverse ideas and perspectives |
| All CJI-adjacent work | 0.0 | Consistency and reproducibility are paramount |

**Regardless of temperature setting, human review of every output remains mandatory.** Temperature is a tool for controlling output style, not output accuracy.

---

## Common Questions and Honest Answers

These questions reflect the concerns that arise when professionals evaluate LLM technology for operational use.

### "If I feed it an incident report, will it make things up?"

**Honest answer:** Probably not in obvious ways, but possibly in subtle ways.

When provided with a complete incident report in the prompt, the model will primarily draw from that text. The vast majority of its output will accurately reflect the source material. However, it may:

- Rephrase in a way that subtly changes meaning
- Add connecting language that implies causation where the source only described correlation
- Select certain details for emphasis based on statistical patterns, not investigative judgment
- Occasionally introduce a detail that sounds right but is not in the source

**Best practice:** Always verify the model's output against the source material. For critical details -- names, dates, times, locations, charges -- check every one against the original document.

### "What's the error rate?"

**Honest answer:** There is no single error rate. It depends on the model, the task, the quality of the prompt, and the complexity of the source material.

To establish a meaningful error rate for a specific deployment:

1. **Create a test set** -- 10-20 representative tasks with known correct answers
2. **Run each task** through the model with the same prompt template
3. **Score the outputs** against the known answers, tracking:
   - Factual errors (wrong names, dates, details)
   - Omissions (important details left out)
   - Fabrications (details not in the source material)
   - Misattributions (correct detail, wrong source or person)
4. **Document the results** as a baseline
5. **Re-test periodically** when models are updated or prompts are changed

This benchmarking process is the only honest way to establish an error rate for a specific use case. Vendor-published benchmarks measure different things (usually general knowledge tasks) and do not translate directly to law enforcement analytical workloads.

For more on building effective prompts that reduce errors, see the [Prompting Guide](06-prompting-guide.md). For specific task examples and their observed performance, see [Use Cases](07-use-cases.md).

### "Is this better or worse than ChatGPT/Claude?"

**Honest answer:** For complex reasoning tasks, commercial models (GPT-4, Claude) at 70B+ parameters significantly outperform local models at the 7B-8B range. For straightforward summarization, drafting, and template-based generation, the gap is smaller than most people expect.

The comparison is not purely about capability:

| Factor | Local 8B Model | Commercial Cloud Model |
|--------|---------------|----------------------|
| Complex reasoning | Weaker | Significantly stronger |
| Simple summarization | Good | Better, but the gap is modest |
| Data privacy | Complete control | Governed by vendor terms and compliance certifications |
| CJI compatibility | Possible with hardening | Requires FedRAMP High + CJIS addendum |
| Cost at scale | Fixed hardware cost | Per-user or per-query recurring cost |
| Internet dependency | None after setup | Required for every query |

The decision is not "which is better" in absolute terms. It is "which fits the operational requirements." See [Why Local LLMs](01-why-local-llms.md) for a detailed comparison of when local deployment makes sense versus when cloud alternatives may be more appropriate.

### "Can I trust it for official products?"

**Honest answer:** Trust the workflow, not the tool. An LLM produces a draft. A qualified human reviews, corrects, and approves that draft. The human -- not the LLM -- is the author of record for any official product.

This is no different from how spell-checkers, grammar tools, or database queries are used today. The tool assists; the professional is responsible for the final product.

For considerations on disclosure, retention, and evidentiary implications of AI-assisted analysis, see [Security Considerations](11-security-considerations.md).

---

## Setting Realistic Expectations

The following table summarizes what local LLMs can and cannot reliably do at the 7B-8B parameter range that runs on standard hardware (see [Hardware Guide](04-hardware-guide.md) for specifications).

### What Local LLMs Do Well

| Task | Why It Works | Typical Quality |
|------|-------------|-----------------|
| Summarization of provided text | Model stays close to source material | 80-90% usable first draft |
| Report template generation | Structured output from structured prompts | High quality with good prompts |
| Draft writing (memos, briefs, BOLOs) | Template-driven generation is a strength | Good first draft, needs editing |
| Pattern identification in provided data | Can surface connections across multiple inputs | Useful leads, requires verification |
| Training scenario generation | Creative generation from parameters | Good variety, adjust for realism |
| Administrative document drafting | Low-stakes, template-based tasks | High quality |

### What Local LLMs Do Poorly

| Task | Why It Struggles | Recommendation |
|------|-----------------|----------------|
| Complex multi-step legal reasoning | Requires capabilities beyond 7B-8B scale | Use for initial research only; legal conclusions require human expertise |
| Mathematical calculations | LLMs are text predictors, not calculators | Never trust LLM math; verify independently |
| Broad world knowledge questions | Limited knowledge in smaller models | Provide all relevant context in the prompt; do not rely on the model's training data |
| Long document analysis (>15-20 pages) | Context window limitations at 7B-8B scale | Break documents into sections and process individually; see [Knowledge Management](08-knowledge-management.md) for RAG approaches |
| Nuanced policy interpretation | Requires deep domain expertise at scale | Use as a drafting assistant only; policy interpretation requires qualified human judgment |

### The 80/20 Framework

A practical way to think about local LLM output:

- **80% of the output** will be usable, accurate, and save significant time compared to starting from scratch
- **20% of the output** will need correction, restructuring, or removal by a qualified reviewer
- **The total workflow** (AI draft + human review) is still significantly faster than manual drafting from scratch
- **The human reviewer** is always the final authority and is accountable for the finished product

This 80/20 split varies by task complexity, prompt quality, and model selection. Simpler tasks with well-structured prompts will see higher usable percentages. Complex analytical tasks will see lower ones.

---

## References

- Huang, L., et al. (2023). "A Survey on Hallucination in Large Language Models." *arXiv:2311.05232*
- Ji, Z., et al. (2023). "Survey of Hallucination in Natural Language Generation." *ACM Computing Surveys*
- Ollama Documentation -- [Temperature and Sampling Parameters](https://github.com/ollama/ollama/blob/main/docs/modelfile.md#parameter)
- NIST AI Risk Management Framework -- [AI 100-1](https://www.nist.gov/artificial-intelligence/ai-risk-management-framework)

---

*This document is part of the [Secure and Affordable In-House AI](../README.md) companion resource. It is an educational resource, not official guidance. Consult your agency's CJIS Systems Officer (CSO) for compliance decisions.*
