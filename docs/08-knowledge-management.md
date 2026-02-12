# Knowledge Management: Connecting Your Data to Local LLMs

> **Summary:** The presentation introduces the concept of feeding personal knowledge to a local LLM -- "your second brain" -- but does not explain the mechanism that makes this work. This document fills that gap. It explains Retrieval Augmented Generation (RAG) in plain language, describes the practical copy-paste workflow that works today, maps the roadmap toward automated RAG integration, and covers personal knowledge management with Obsidian. Understanding this bridge between data and inference is what makes the entire local LLM value proposition operational.

---

## Table of Contents

- [The Problem This Document Addresses](#the-problem-this-document-addresses)
- [What Is RAG (Retrieval Augmented Generation)](#what-is-rag-retrieval-augmented-generation)
- [Current Reality: The Manual Copy-Paste Workflow](#current-reality-the-manual-copy-paste-workflow)
- [The Roadmap: Automated RAG Integration](#the-roadmap-automated-rag-integration)
- [Personal Knowledge Management with Obsidian](#personal-knowledge-management-with-obsidian)
- [Putting It Together: Data Strategy by Phase](#putting-it-together-data-strategy-by-phase)
- [Key Links](#key-links)
- [References](#references)

---

## The Problem This Document Addresses

The presentation introduces a compelling idea: feed personal notes, case knowledge, institutional memory, and analytical frameworks into a local LLM, transforming it from a generic text generator into a contextual assistant that "knows" what the analyst knows. This is the "second brain" concept -- an AI system augmented by personal and organizational knowledge.

The idea is sound. But the presentation skips the most important question: **how does the data actually get from a collection of notes and documents into the LLM's context?**

A local LLM (Ollama running Llama 3.1, for example) does not have access to files on the hard drive by default. It does not read Obsidian vaults, browse local directories, or index documents automatically. It processes only what is explicitly provided in each prompt. Without a mechanism to bridge the gap between stored documents and the LLM's inference engine, "feed your data to the AI" remains an aspiration rather than a workflow.

This document explains the three levels of that bridge:

| Level | Approach | Complexity | Phase |
|-------|----------|-----------|-------|
| **Level 1** | Manual copy-paste into prompts | None -- works today | Phase 1 |
| **Level 2** | Document upload via Open WebUI | Low -- built into existing tools | Phase 1-2 |
| **Level 3** | Automated RAG pipeline | Moderate -- requires additional tooling | Phase 2+ |

Each level builds on the previous one. Most deployments should start at Level 1 and advance only when the workflow demands it.

---

## What Is RAG (Retrieval Augmented Generation)

RAG is the technical mechanism that connects a collection of documents to an LLM. Understanding RAG, even at a conceptual level, is essential for anyone evaluating whether local LLMs can meet their analytical needs.

### The Core Concept

A standard LLM answers questions based on its training data -- the billions of words it processed during model training, which ended at a specific cutoff date. It has no knowledge of documents on the local machine, recent events, or organization-specific information.

RAG changes this by inserting a retrieval step before the LLM generates its answer. Instead of answering from training data alone, the system first searches through a collection of provided documents, finds the passages most relevant to the question, and then feeds those passages into the LLM's prompt as context. The LLM generates its response based on the retrieved context plus its general language capabilities.

**In plain language:** RAG is an automated way to find the right page in the right document and paste it into the prompt before the AI answers.

### How RAG Works -- Step by Step

```
                YOUR DOCUMENTS
        (Obsidian vault / local files / reports)
                      |
                      v
          +------------------------+
          |  STEP 1: INDEXING      |
          |  (one-time setup)      |
          |                        |
          |  Documents are split   |
          |  into chunks and       |
          |  converted into        |
          |  numerical vectors     |
          |  (embeddings)          |
          +------------------------+
                      |
                      v
          +------------------------+
          |  VECTOR DATABASE       |
          |  (searchable index)    |
          |                        |
          |  Stores document       |
          |  chunks as vectors     |
          |  for fast similarity   |
          |  search                |
          +------------------------+
                      ^
                      |
              USER ASKS A QUESTION
                      |
                      v
          +------------------------+
          |  STEP 2: RETRIEVAL     |
          |  (every query)         |
          |                        |
          |  Question is converted |
          |  to a vector and       |
          |  compared against the  |
          |  document index        |
          |                        |
          |  Most relevant chunks  |
          |  are selected          |
          +------------------------+
                      |
                      v
          +------------------------+
          |  STEP 3: GENERATION    |
          |  (every query)         |
          |                        |
          |  Retrieved chunks +    |
          |  original question     |
          |  are combined into a   |
          |  prompt and sent to    |
          |  Ollama                |
          |                        |
          |  LLM generates answer  |
          |  BASED ON the          |
          |  retrieved context     |
          +------------------------+
                      |
                      v
               RESPONSE TO USER
        (grounded in your documents)
```

### Why RAG Matters

RAG addresses three fundamental limitations of standalone LLMs:

| Limitation | Without RAG | With RAG |
|-----------|-------------|----------|
| **Knowledge cutoff** | Model only knows its training data (often months or years old) | Model answers from current documents provided at query time |
| **Hallucination** | Model generates plausible-sounding text that may not be factually grounded | Model generates text grounded in specific retrieved passages, significantly reducing hallucination (though not eliminating it entirely) |
| **Organizational knowledge** | Model has no knowledge of agency-specific procedures, cases, or context | Model can reference agency documents, SOPs, and analytical products |

**Important caveat:** RAG reduces hallucination but does not eliminate it. The model can still misinterpret retrieved passages, combine information from multiple passages incorrectly, or generate text that sounds like it came from the documents but is actually a confabulation. Human review remains mandatory. See [Understanding LLMs](02-understanding-llms.md) for more on hallucination.

### Key RAG Terminology

| Term | Definition |
|------|-----------|
| **Embedding** | A numerical representation (vector) of a piece of text. Similar texts have similar embeddings, enabling semantic search. |
| **Vector database** | A specialized database optimized for storing and searching embeddings. Examples: ChromaDB, FAISS, Qdrant. |
| **Chunk** | A segment of a document (typically 200-1000 words). Documents are split into chunks because most LLMs cannot process entire large documents at once. |
| **Semantic search** | Finding documents by meaning rather than exact keyword match. "Officer safety incident" can match passages about "use of force events" even without those exact words. |
| **Context window** | The maximum amount of text an LLM can process in a single prompt. Retrieved chunks must fit within this window alongside the question and instructions. |
| **Retrieval** | The process of searching the vector database to find chunks relevant to the user's question. |

---

## Current Reality: The Manual Copy-Paste Workflow

For most initial deployments, RAG infrastructure is not necessary. The practical day-one workflow is manual: identify the relevant content, copy it into the prompt, and let the LLM process it.

### How the Manual Workflow Works

```
Step 1: Analyst identifies relevant documents
        (case file section, SOP excerpt, previous analysis)
                        |
                        v
Step 2: Analyst copies relevant text
        (Ctrl+C from the source document)
                        |
                        v
Step 3: Analyst pastes into the LLM prompt
        (along with the task instructions)
                        |
                        v
Step 4: LLM processes the provided context + task
                        |
                        v
Step 5: Analyst reviews and refines the output
```

### Advantages of the Manual Approach

| Advantage | Detail |
|-----------|--------|
| **Zero additional tooling** | Works with Ollama and Open WebUI as-is, no configuration required |
| **Complete human control** | The analyst decides exactly what context enters the prompt |
| **No indexing required** | No need to pre-process or index any documents |
| **Immediate value** | Available from the first day of deployment |
| **Transparency** | The analyst knows exactly what data the model is working with |

### Limitations of the Manual Approach

| Limitation | Detail |
|-----------|--------|
| **Manual effort** | Finding and copying relevant content takes time, especially across multiple documents |
| **Context window constraints** | Only as much text as fits in the model's context window can be included (typically 4K-8K tokens for 7B-13B models, roughly 3,000-6,000 words) |
| **Does not scale** | Practical for a handful of documents; impractical for searching across hundreds or thousands |
| **No persistent indexing** | Each session starts from zero -- there is no accumulated knowledge base the model can search |
| **Analyst must know what to include** | The quality of the output depends on the analyst selecting the right context |

### When Manual Copy-Paste Is Sufficient

The manual approach works well when:

- The analyst already knows which documents are relevant
- The relevant content fits within a few thousand words
- The task involves a small number of source documents (1-5)
- The deployment is in Phase 1 (non-CJI pilot) and the priority is proving value

This is where most organizations start, and it is a perfectly valid long-term approach for many workflows. The use cases in [Use Cases](07-use-cases.md) are all designed around this manual approach.

### Open WebUI Document Upload (Level 2)

Open WebUI includes a built-in document upload feature that serves as a middle ground between fully manual copy-paste and a complete RAG pipeline. When a user uploads a document through the Open WebUI interface, the system:

1. Processes the document into chunks
2. Creates embeddings using a local embedding model
3. Stores the chunks in a local vector database
4. Automatically retrieves relevant chunks when the user asks questions

This is, functionally, a simplified RAG pipeline built into the chat interface. It eliminates the manual copy-paste step for documents that have been uploaded.

**Advantages:**
- No additional tools to install beyond Open WebUI (which is recommended as the primary interface -- see [Ollama Quickstart](03-ollama-quickstart.md))
- Upload once, query multiple times
- Semantic search across uploaded documents

**Limitations:**
- Document processing quality varies (PDFs with complex formatting may not parse cleanly)
- Limited control over chunking strategy and embedding model
- Not designed for large-scale document collections (hundreds or thousands of files)
- Uploaded documents persist only within Open WebUI's local database

**When to use this:** Open WebUI document upload is the recommended second step after the manual workflow. Once an analyst finds themselves repeatedly copying the same documents into prompts, uploading those documents to Open WebUI saves time while maintaining simplicity.

---

## The Roadmap: Automated RAG Integration

Automated RAG is a Phase 2+ consideration. It is not required for initial deployment or for demonstrating value. However, understanding the options is important for planning.

### Why Upgrade to Automated RAG

The case for automated RAG emerges when:

- Analysts need to search across large document collections (hundreds of files or more)
- The same knowledge base is queried repeatedly by multiple users
- The manual workflow becomes a bottleneck -- more time is spent finding context than reviewing output
- The deployment moves beyond individual use toward team-level knowledge management

### Option 1: AnythingLLM

**What it is:** A dedicated local RAG platform designed specifically to connect document collections to local LLMs. Open source (MIT license).

| Feature | Detail |
|---------|--------|
| **Document ingestion** | Supports PDF, DOCX, TXT, CSV, and web page import |
| **Vector database** | Built-in (LanceDB) or external (ChromaDB, Pinecone, Qdrant) |
| **LLM backend** | Connects directly to Ollama |
| **Workspace model** | Documents organized into workspaces with separate indexes |
| **User interface** | Chat-based, similar to ChatGPT |
| **Deployment** | Local Docker container or native install |

**Strengths:** Purpose-built for the local RAG use case. Provides workspace isolation (different document sets for different projects). Straightforward setup for users already running Ollama.

**Limitations:** Another application to deploy and maintain. Adds complexity to the technology stack. Workspace management requires deliberate organizational structure.

**Repository:** [github.com/Mintplex-Labs/anything-llm](https://github.com/Mintplex-Labs/anything-llm)

### Option 2: PrivateGPT

**What it is:** A local RAG platform focused on privacy and data sovereignty. Open source (Apache 2.0 license). Designed from the ground up for environments where data must never leave the local machine.

| Feature | Detail |
|---------|--------|
| **Document ingestion** | PDF, DOCX, TXT, and other common formats |
| **Vector database** | Built-in (Qdrant or ChromaDB) |
| **LLM backend** | Supports Ollama and other local backends |
| **Privacy focus** | All processing is local -- no telemetry, no external API calls |
| **API** | OpenAI-compatible API for integration with other tools |
| **Deployment** | Local installation |

**Strengths:** Strong privacy posture aligns with law enforcement data handling requirements. API compatibility enables integration with other tools. Active development community.

**Limitations:** Setup is more involved than AnythingLLM. Documentation assumes some technical familiarity. Feature set is narrower than AnythingLLM.

**Repository:** [github.com/zylon-ai/private-gpt](https://github.com/zylon-ai/private-gpt)

### Option 3: Obsidian LLM Plugins

For analysts already using Obsidian for knowledge management (see next section), several community plugins connect the Obsidian vault directly to a local LLM.

**Smart Connections** is the most mature plugin in this space. It creates embeddings of Obsidian notes and enables semantic search and AI-assisted queries across the vault.

| Feature | Detail |
|---------|--------|
| **Integration** | Runs inside Obsidian -- no separate application |
| **Indexing** | Embeds all notes in the vault for semantic search |
| **LLM connection** | Connects to Ollama for local inference |
| **Workflow** | Ask questions about notes; get answers grounded in vault content |
| **Privacy** | All processing local when using Ollama backend |

**Strengths:** No additional application to deploy or manage. The analyst's existing Obsidian workflow becomes the knowledge base. Natural integration between note-taking and AI-assisted analysis.

**Limitations:** Requires Obsidian (which the presentation recommends). Plugin quality and maintenance depend on community contributors. Less robust than dedicated RAG platforms for large-scale document collections. Limited to content within the Obsidian vault.

### RAG Option Comparison

| Feature | Open WebUI Upload | AnythingLLM | PrivateGPT | Obsidian Plugins |
|---------|-------------------|-------------|------------|------------------|
| **Setup complexity** | None (built-in) | Low-moderate | Moderate | Low |
| **Additional software** | None | Docker container | Local install | Obsidian plugin |
| **Document scale** | Dozens | Hundreds-thousands | Hundreds-thousands | Vault-sized |
| **Multi-user** | Yes (Open WebUI) | Yes (workspaces) | Yes (API) | No (single user) |
| **Best for** | Getting started | Team knowledge base | Privacy-critical | Individual analyst |
| **CJI considerations** | Same as Ollama deployment | Requires same hardening | Requires same hardening | Single-user, local only |

**Recommendation for Phase 1:** Use the manual copy-paste workflow and Open WebUI document upload. These require no additional tooling and provide immediate value.

**Recommendation for Phase 2+:** Evaluate AnythingLLM or PrivateGPT if the deployment scales beyond individual use or if analysts need to search across large document collections. The choice between them depends on whether workspace management (AnythingLLM) or API integration (PrivateGPT) is the higher priority.

**Important CJIS note:** Any RAG platform that ingests, indexes, or stores CJI must meet the same CJIS compliance requirements as the LLM itself. The vector database, document store, and embedding index all constitute CJI processing and storage. See the [CJIS Compliance Framework](09-cjis-compliance.md) for requirements. During the non-CJI pilot (Phase 1), this is not a concern. During CJI production (Phase 4), it is critical.

---

## Personal Knowledge Management with Obsidian

The presentation references Obsidian as the "second brain" for building a personal knowledge repository. This section explains what Obsidian is, why it is well-suited for law enforcement professionals, and how it connects to the local LLM workflow.

### What Is Obsidian

Obsidian is a free, local-first note-taking application that stores all data as plain-text Markdown files in a folder on the local machine. There is no cloud dependency, no account required (for local use), and no vendor lock-in. Notes are interlinked, searchable, and organized however the user prefers.

| Feature | Detail |
|---------|--------|
| **Data format** | Plain-text Markdown (.md files) |
| **Storage** | Local folder (the "vault") -- user controls where files live |
| **Cost** | Free for personal use; paid sync is optional and not required |
| **Linking** | Notes can link to other notes, creating a knowledge graph |
| **Search** | Full-text search across all notes, with tag and metadata filtering |
| **Plugins** | Extensible via community plugins (500+), including LLM integration |
| **Platforms** | Windows, macOS, Linux, iOS, Android |

### Why Obsidian for Law Enforcement Professionals

| Benefit | Relevance |
|---------|-----------|
| **All data stays local** | Notes never leave the machine unless the user explicitly exports or syncs them. No cloud storage by default. This is critical for sensitive (but non-CJI) analytical notes. |
| **Plain-text durability** | Markdown files are readable by any text editor. If Obsidian disappears tomorrow, the notes remain accessible. No proprietary format lock-in. |
| **Linking and graph view** | Connecting notes about related subjects, locations, patterns, and cases creates a visual knowledge graph that reveals relationships across analytical products. |
| **Tagging and metadata** | Notes can be tagged by case, subject, crime type, jurisdiction, or any other taxonomy. Combined with search, this enables rapid retrieval across years of accumulated knowledge. |
| **Templates** | Obsidian supports templates for standardized notes (daily briefing template, case note template, meeting notes template), ensuring consistency across the knowledge base. |
| **Free and self-contained** | No budget request required. No IT approval needed for a note-taking application. Low barrier to entry. |

### Building a Knowledge Repository

A well-organized Obsidian vault can serve as the foundation for the LLM-augmented "second brain" the presentation describes. The following structure is one approach -- not prescriptive, but illustrative:

```
Analyst Vault/
|
|-- Daily Notes/
|   |-- 2025-01-13.md          (daily observations, briefing notes)
|   |-- 2025-01-14.md
|
|-- Cases/
|   |-- Case-2025-001.md       (case-specific notes and links)
|   |-- Case-2025-002.md
|
|-- Intelligence/
|   |-- Patterns/
|   |   |-- Retail-Theft-Ring.md
|   |   |-- Vehicle-Break-In-Series.md
|   |-- Subjects/
|   |   |-- (subject tracking notes)
|   |-- Areas/
|       |-- District-3-Trends.md
|
|-- Procedures/
|   |-- Search-Warrant-Checklist.md
|   |-- Evidence-Submission-Process.md
|
|-- Training/
|   |-- Interview-Techniques.md
|   |-- Report-Writing-Standards.md
|
|-- Templates/
|   |-- Daily-Note-Template.md
|   |-- Case-Note-Template.md
|   |-- Intelligence-Product-Template.md
|
|-- Prompts/
    |-- Situational-Awareness-Brief.md
    |-- Case-Summary-Prompt.md
    |-- OSINT-Synthesis-Prompt.md
```

### How Obsidian Connects to Local LLMs

The connection between Obsidian and Ollama can operate at three levels:

**Level 1: Manual reference (today)**
The analyst uses Obsidian to organize notes and knowledge, then manually copies relevant content into LLM prompts. Obsidian serves as the organized retrieval system; the human serves as the retrieval mechanism.

**Level 2: Plugin-assisted (near-term)**
Obsidian plugins like Smart Connections create embeddings of vault content and enable semantic search within Obsidian, with the option to send relevant notes directly to Ollama for processing. The plugin acts as a simplified RAG layer within the Obsidian interface.

**Level 3: Full RAG pipeline (future)**
A dedicated RAG platform (AnythingLLM, PrivateGPT) indexes the Obsidian vault as a document source, enabling automated retrieval across all vault content when querying the LLM. The analyst asks a question; the system finds the relevant notes and provides them as context automatically.

### A Note on What Belongs in Obsidian

Obsidian is a note-taking and knowledge management tool, not a records management system. It is appropriate for:

- Personal analytical notes and observations
- Collected open-source information
- Procedural references and checklists
- Prompt templates and LLM workflow documentation
- Training notes and professional development
- Non-CJI intelligence products and assessments

It is **not** appropriate as a repository for CJI, original case files, evidence, or official records. Those belong in the agency's authorized records management system. Obsidian notes should reference case numbers and link to official records but should not contain CJI content -- particularly if the vault is not stored on a CJIS-compliant system.

---

## Putting It Together: Data Strategy by Phase

The following table maps the knowledge management approach to the deployment phases described in the [CJIS Compliance Framework](09-cjis-compliance.md):

| Phase | Knowledge Management Approach | Tools | CJI Status |
|-------|-------------------------------|-------|-----------|
| **Phase 1: Non-CJI Pilot** | Manual copy-paste + Open WebUI document upload | Ollama, Open WebUI, Obsidian | Non-CJI only |
| **Phase 2: Security Hardening** | Begin evaluating RAG platforms; test with non-CJI documents | Add AnythingLLM or PrivateGPT evaluation | Non-CJI only |
| **Phase 3: Compliance Validation** | Validate RAG platform against CJIS requirements if CJI ingestion is planned | Include RAG platform in security assessment | Pre-validation |
| **Phase 4: CJI Production** | Deploy hardened RAG pipeline for CJI documents (if validated) | Full stack with CJIS controls | CJI authorized |
| **Phase 5: Scale** | Expand document collections, multi-user knowledge bases, advanced workflows | Team-level RAG deployment | CJI authorized |

**Key principle:** The knowledge management tooling advances in lockstep with the compliance posture. Do not ingest CJI into any system -- including a RAG vector database -- before that system has been hardened and validated to the same standard as the LLM itself.

---

## Key Links

| Resource | URL | Description |
|----------|-----|-------------|
| **Obsidian** | [obsidian.md](https://obsidian.md) | Free, local-first knowledge management |
| **Open WebUI** | [github.com/open-webui/open-webui](https://github.com/open-webui/open-webui) | Chat interface for Ollama with built-in document upload |
| **AnythingLLM** | [github.com/Mintplex-Labs/anything-llm](https://github.com/Mintplex-Labs/anything-llm) | Local RAG platform with workspace management |
| **PrivateGPT** | [github.com/zylon-ai/private-gpt](https://github.com/zylon-ai/private-gpt) | Privacy-focused local RAG platform |
| **Smart Connections** | [github.com/brianpetro/obsidian-smart-connections](https://github.com/brianpetro/obsidian-smart-connections) | Obsidian plugin for LLM integration |
| **ChromaDB** | [trychroma.com](https://www.trychroma.com/) | Open-source embedding database |
| **Ollama** | [ollama.com](https://ollama.com) | Local LLM runtime |

---

## References

- [Ollama](https://ollama.com) -- Local LLM runtime
- [Open WebUI](https://github.com/open-webui/open-webui) -- Chat interface with built-in RAG capability
- [AnythingLLM](https://github.com/Mintplex-Labs/anything-llm) -- Local RAG platform
- [PrivateGPT](https://github.com/zylon-ai/private-gpt) -- Privacy-focused local RAG
- [Obsidian](https://obsidian.md) -- Local-first knowledge management
- Lewis, P., et al. "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks." NeurIPS 2020. -- The original RAG paper.
- FBI CJIS Security Policy v6.0 -- [CJIS Resource Center](https://www.fbi.gov/services/cjis)

---

*This document is part of the [Secure and Affordable In-House AI](../README.md) companion resource. It is an educational resource, not official guidance. Consult your agency's CJIS Systems Officer (CSO) for compliance decisions.*
