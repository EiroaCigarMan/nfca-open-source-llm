# Ollama Quickstart -- Your First 30 Minutes

> **Summary:** Ollama is a free, open-source local LLM runtime that turns a standard workstation into an AI assistant. It installs with a single command, runs models locally with no internet dependency after setup, and exposes an OpenAI-compatible API for tool integration. This guide walks through installation, first model pull, first prompt, and setting up a browser-based chat interface -- all within the first 30 minutes.

---

## Table of Contents

- [What Is Ollama](#what-is-ollama)
- [First 30 Minutes Quickstart](#first-30-minutes-quickstart)
- [Why Ollama Over Alternatives](#why-ollama-over-alternatives)
- [GUI Options for Non-CLI Users](#gui-options-for-non-cli-users)
- [Post-Setup Verification](#post-setup-verification)
- [Key Links](#key-links)
- [References](#references)

---

## What Is Ollama

Ollama is an open-source local LLM runtime released under the MIT License. It provides a single binary that handles model management, inference, and API serving -- no complex configuration, no Python environments, no dependency management.

### Key Characteristics

| Feature | Detail |
|---------|--------|
| **License** | MIT (fully open source, no usage restrictions) |
| **Cost** | Free -- no subscription, no per-query charges, no vendor lock-in |
| **Installation** | Single command on Linux/macOS; installer on Windows |
| **Model library** | 200+ models available through `ollama pull` |
| **API** | OpenAI-compatible REST API on `localhost:11434` |
| **GPU support** | NVIDIA CUDA (primary), AMD ROCm (experimental), Apple Metal (macOS) |
| **Internet required** | Only for initial model download; fully offline after setup |

### What Ollama Does

- **Downloads and manages models** -- A single `ollama pull` command downloads a model and handles all file management
- **Runs inference locally** -- Prompts and responses never leave the machine
- **Serves an API** -- Any tool that speaks the OpenAI API format can connect to Ollama
- **Manages GPU memory** -- Automatically loads and unloads models based on available VRAM

### What Ollama Does NOT Do

- **No built-in authentication** -- Anyone with network access to port 11434 can query the model (see [CJIS Compliance](09-cjis-compliance.md) for security hardening)
- **No audit logging** -- Prompts and responses are not logged by default
- **No user interface** -- Ollama is a command-line and API tool; a separate interface is needed for chat-style interaction
- **No encryption** -- API traffic is HTTP by default, not HTTPS

These gaps are addressable with additional tooling. See [CJIS Compliance](09-cjis-compliance.md) for the complete security hardening framework.

---

## First 30 Minutes Quickstart

### Prerequisites

- A workstation meeting the specifications in the [Hardware Guide](04-hardware-guide.md) (minimum: 16GB VRAM GPU recommended)
- Administrator/root access for installation
- Internet connectivity for initial setup (models are typically 4-8 GB each)
- Approximately 20 GB of free disk space for Ollama and initial models

### Step 1: Install Ollama (5 minutes)

**Linux (recommended for server/workstation deployment):**

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

This downloads and installs the Ollama binary, creates a systemd service, and starts Ollama automatically.

**macOS:**

Download the installer from [ollama.com/download](https://ollama.com/download) or install via Homebrew:

```bash
brew install ollama
```

**Windows:**

Download the installer from [ollama.com/download](https://ollama.com/download) and run it. The installer creates a system service that starts automatically.

**Verify the installation:**

```bash
ollama --version
```

Expected output: a version number (e.g., `ollama version 0.6.2`). If this command returns a version, installation succeeded.

### Step 2: Pull Your First Model (5-10 minutes)

Download the Llama 3.1 8B model -- a strong general-purpose model that fits comfortably on a 16GB VRAM GPU:

```bash
ollama pull llama3.1:8b
```

This downloads approximately 4.7 GB of model data. Download time depends on internet connection speed.

**What to expect:**

```
pulling manifest
pulling 8eeb52dfb3bb... 100% ▕████████████████████████▏ 4.7 GB
pulling 948af2743fc7... 100% ▕████████████████████████▏ 1.5 KB
pulling 0ba8f0e314b4... 100% ▕████████████████████████▏  12 KB
pulling 56bb8bd477a5... 100% ▕████████████████████████▏   96 B
pulling 1a4c3c319823... 100% ▕████████████████████████▏  485 B
verifying sha256 digest
writing manifest
success
```

### Step 3: Run Your First Prompt (2 minutes)

Test the model with a simple prompt:

```bash
ollama run llama3.1:8b "Summarize the key points of the Fourth Amendment in plain language."
```

The model should generate a clear, readable summary of Fourth Amendment protections. This output is generated entirely on the local machine -- no data was sent to any external server.

**Try a more practical example:**

```bash
ollama run llama3.1:8b "Draft a 3-sentence summary of this incident: On January 15, officers responded to a report of a burglary at 123 Main Street. The complainant stated they left for work at 0700 and returned at 1730 to find the rear door forced open. A television, laptop, and jewelry were reported missing. No witnesses have been identified."
```

### Step 4: Verify It Works (2 minutes)

Run these verification checks:

**Check that the model is available:**

```bash
ollama list
```

Expected output: a table showing `llama3.1:8b` with its size and modification date.

**Check that the API is responding:**

```bash
curl http://localhost:11434/api/tags
```

Expected output: a JSON response listing available models. If this returns data, the Ollama API is running and accessible.

**Check GPU utilization (NVIDIA):**

```bash
nvidia-smi
```

When a model is loaded, this should show Ollama using GPU memory. If the model is loaded in CPU mode instead, check that NVIDIA drivers and CUDA are properly installed.

### Step 5: Pull Additional Models (5-10 minutes)

Pull one or two additional models for comparison. See [Model Selection](05-model-selection.md) for detailed recommendations.

```bash
# Strong general-purpose alternative
ollama pull mistral:7b

# Strong reasoning model
ollama pull deepseek-r1:8b

# Lightweight model for fast, simple tasks
ollama pull phi3:mini
```

At this point, the command-line setup is complete. The system can process prompts entirely offline.

---

## Why Ollama Over Alternatives

Several open-source tools can run LLMs locally. Ollama is recommended as the starting point for law enforcement deployments based on its combination of simplicity, breadth, and community support.

### Comparison Table

| Tool | Type | Ease of Setup | Model Library | API Support | Best For |
|------|------|--------------|---------------|-------------|----------|
| **Ollama** | Runtime + model manager | Single command install | 200+ models via `ollama pull` | OpenAI-compatible | First deployment; broadest compatibility |
| **LM Studio** | Desktop GUI application | Download and run | Hugging Face model browser | OpenAI-compatible | Users who want a desktop application |
| **llama.cpp** | Low-level inference engine | Requires compilation | Manual model download | Custom API | Maximum performance tuning; advanced users |
| **vLLM** | High-throughput server | Python environment setup | Manual model loading | OpenAI-compatible | Multi-user server deployments at scale |
| **LocalAI** | API-first runtime | Docker deployment | Multiple format support | OpenAI-compatible | Drop-in replacement for OpenAI API |

### Why Ollama Is the Recommended Starting Point

1. **Simplest on-ramp** -- One install command, one pull command, one run command. No Python environments, no Docker, no compilation.
2. **Broadest model library** -- The `ollama.com/library` provides pre-quantized models ready to pull. No need to navigate Hugging Face, convert model formats, or manage quantization.
3. **Active community** -- Large user base means more troubleshooting resources, more tutorials, and faster issue resolution.
4. **API compatibility** -- The OpenAI-compatible API means tools built for ChatGPT's API can often connect to Ollama with a URL change.
5. **Cross-platform** -- Works on Linux, macOS, and Windows with the same commands.

### Important Context

No open-source LLM runtime ships with CJIS compliance built in. The compliance question is not "which tool is compliant" but "which tool is easiest to harden." Ollama's simple architecture -- a single binary serving a REST API -- is straightforward to place behind a reverse proxy with authentication, TLS, and logging. See [CJIS Compliance](09-cjis-compliance.md) for the complete hardening framework.

---

## GUI Options for Non-CLI Users

Most analysts prefer a familiar chat interface over a command-line terminal. Open WebUI provides a ChatGPT-like browser interface that connects directly to Ollama.

### Open WebUI

**What it is:** A self-hosted web interface for Ollama that looks and feels like ChatGPT. It runs as a local web application and connects to the Ollama API.

**Key features:**

| Feature | Detail |
|---------|--------|
| **Interface** | Browser-based chat; supports conversations, history, and multiple models |
| **Document upload** | Upload files directly into conversations for analysis |
| **Model switching** | Switch between installed models within the same interface |
| **Conversation history** | Saves chat history locally (stored in a local database) |
| **Multi-user support** | Supports multiple user accounts with separate conversation histories |
| **RAG integration** | Built-in document ingestion for retrieval-augmented generation |
| **License** | MIT (open source) |

### Setting Up Open WebUI

**Option 1: Docker (recommended)**

If Docker is installed:

```bash
docker run -d -p 3000:8080 \
  --add-host=host.docker.internal:host.docker.internal \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  ghcr.io/open-webui/open-webui:main
```

Then open `http://localhost:3000` in a browser. Create an admin account on first launch.

**Option 2: pip install (if Docker is unavailable)**

```bash
pip install open-webui
open-webui serve
```

Then open `http://localhost:8080` in a browser.

**Option 3: Docker with bundled Ollama (single-container deployment)**

For environments where a single container is preferred:

```bash
docker run -d -p 3000:8080 \
  --gpus=all \
  -v ollama:/root/.ollama \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  ghcr.io/open-webui/open-webui:ollama
```

### Why a GUI Matters

The command line is efficient for IT staff and power users. For most analysts:

- A chat interface is familiar and intuitive -- it looks like the ChatGPT and messaging tools they already use
- Conversation history allows revisiting previous analyses
- Document upload simplifies the process of feeding data to the model
- Lower barrier to adoption means faster organizational value

Deploying Open WebUI alongside Ollama is strongly recommended for any multi-user deployment or any deployment where non-technical users will interact with the system.

### Security Note

Open WebUI stores conversation history in a local SQLite database. For deployments that will eventually process CJI, this database becomes a CJI-containing asset and must be protected accordingly. See [CJIS Compliance](09-cjis-compliance.md), specifically Gap 5 (Encryption at Rest) and Gap 8 (Media Protection), for guidance.

---

## Post-Setup Verification

After completing the quickstart, verify the deployment with this checklist:

| Check | Command | Expected Result |
|-------|---------|-----------------|
| Ollama is running | `ollama --version` | Version number displayed |
| Model is installed | `ollama list` | Lists `llama3.1:8b` (and any other pulled models) |
| API is accessible | `curl http://localhost:11434/api/tags` | JSON response with model list |
| Model generates output | `ollama run llama3.1:8b "Hello"` | Model responds with text |
| GPU is being used | `nvidia-smi` | Shows Ollama process using VRAM |
| Open WebUI loads (if installed) | Open `http://localhost:3000` in browser | Login page or chat interface appears |

If all checks pass, the system is ready for non-CJI workloads. For CJI workloads, complete the security hardening described in [CJIS Compliance](09-cjis-compliance.md) before processing any criminal justice information.

---

## Key Links

| Resource | URL |
|----------|-----|
| Ollama Official Site | https://ollama.com |
| Ollama GitHub Repository | https://github.com/ollama/ollama |
| Ollama Model Library | https://ollama.com/library |
| Ollama API Documentation | https://github.com/ollama/ollama/blob/main/docs/api.md |
| Open WebUI GitHub | https://github.com/open-webui/open-webui |
| Open WebUI Documentation | https://docs.openwebui.com |

---

## References

- Ollama Documentation -- [GitHub](https://github.com/ollama/ollama)
- Open WebUI Documentation -- [GitHub](https://github.com/open-webui/open-webui)
- Ollama Model Library -- [ollama.com/library](https://ollama.com/library)

---

*This document is part of the [Secure and Affordable In-House AI](../README.md) companion resource. It is an educational resource, not official guidance. Consult your agency's CJIS Systems Officer (CSO) for compliance decisions.*
