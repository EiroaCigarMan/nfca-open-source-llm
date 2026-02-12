# Hardware Guide -- Requirements, Model Mapping, and Cost Analysis

> **Summary:** A capable local AI workstation costs approximately $1,000-$1,500 in hardware. The GPU is the most critical component -- its VRAM determines which models can run and at what speed. This guide provides specific hardware recommendations, maps models to hardware requirements so the capability boundaries are clear, compares total cost of ownership against cloud alternatives, and identifies the hidden costs that are easy to overlook in budget planning.

---

## Table of Contents

- [Recommended Hardware Specifications](#recommended-hardware-specifications)
- [What Your Hardware Can Actually Run](#what-your-hardware-can-actually-run)
- [Honest Cost Comparison](#honest-cost-comparison)
- [GPU Alternatives and Considerations](#gpu-alternatives-and-considerations)
- [Hidden Costs to Budget For](#hidden-costs-to-budget-for)
- [Procurement Guidance](#procurement-guidance)
- [References](#references)

---

## Recommended Hardware Specifications

The following specification targets a workstation that can run 7B-8B parameter models at full speed with room for larger quantized models. This represents the best balance of capability, cost, and availability using standard commercial components.

### Recommended Build

| Component | Specification | Estimated Cost | Why This Matters |
|-----------|--------------|----------------|------------------|
| **GPU** | NVIDIA RTX 4060 Ti 16GB VRAM | $400-600 | VRAM is the primary bottleneck; 16GB runs all 7B-8B models comfortably |
| **RAM** | 32GB DDR5 (2x16GB) | $80-120 | Models that exceed VRAM spill to system RAM; 32GB provides headroom |
| **CPU** | Intel Core i5/i7 12th gen+ or AMD Ryzen 5/7 5000+ | $150-250 | Model loading and prompt processing benefit from modern CPUs; not the primary bottleneck |
| **Storage** | 1TB NVMe SSD | $70-100 | Models are 4-8 GB each; NVMe speeds reduce load times |
| **PSU** | 650-750W 80+ Bronze or better | $70-100 | GPU under load draws significant power; adequate PSU prevents instability |
| **Motherboard** | Compatible ATX motherboard | $100-150 | Must support CPU socket and have PCIe 4.0 x16 slot for GPU |
| **Case** | Standard ATX mid-tower | $50-100 | Adequate airflow for GPU cooling; nothing exotic required |
| **Total** | | **$1,000-$1,500** | Complete workstation, excluding monitor, keyboard, and mouse |

### Why 16GB VRAM Is the Target

VRAM (Video RAM on the GPU) is the single most important specification for local LLM deployment. The entire model must fit in VRAM for full-speed inference. When a model exceeds available VRAM, it "spills" to system RAM, which is 10-50x slower.

- **8GB VRAM:** Can run 3B-7B quantized models. Tight fit; limited headroom for context.
- **16GB VRAM:** Comfortably runs all 7B-8B models with room for large context windows. The recommended target.
- **24GB VRAM:** Can run some 13B models and heavily quantized 30B models. Costs $800-1,200 for the GPU alone.
- **48GB+ VRAM:** Required for 70B+ models at usable speed. Professional/datacenter GPUs at $2,000+.

The RTX 4060 Ti 16GB represents the best price-to-VRAM ratio for this workload as of early 2025.

---

## What Your Hardware Can Actually Run

This is the most important table in this guide. It maps specific models to their VRAM requirements and indicates which ones fit on the recommended hardware.

### Model-Hardware Compatibility Table

| Model | Parameters | VRAM Required (Q4 Quantization) | Fits on 16GB GPU? | Ollama Pull Command | Strengths |
|-------|-----------|-------------------------------|-------------------|---------------------|-----------|
| **Phi-3 Mini** | 3.8B | ~3 GB | Yes | `ollama pull phi3:mini` | Lightweight, fast responses, limited capability |
| **Mistral 7B** | 7B | ~5 GB | Yes | `ollama pull mistral:7b` | Strong general-purpose, good instruction following |
| **Llama 3.1 8B** | 8B | ~6 GB | Yes | `ollama pull llama3.1:8b` | Best general-purpose at this size, strong drafting |
| **Qwen3-8B** | 8B | ~6 GB | Yes | `ollama pull qwen3:8b` | Strong reasoning, multilingual capability |
| **DeepSeek-R1 8B** | 8B | ~6 GB | Yes | `ollama pull deepseek-r1:8b` | Strong reasoning and chain-of-thought |
| **Llama 3.1 13B** | 13B | ~9 GB | Yes (tight) | `ollama pull llama3.1:13b` | Better quality than 8B across all tasks |
| **Mixtral 8x7B** | 46.7B (MoE) | ~26 GB | **No** | `ollama pull mixtral:8x7b` | High quality but requires 24GB+ GPU or dual GPUs |
| **Llama 3.1 70B** | 70B | ~40 GB | **No** | `ollama pull llama3.1:70b` | Near commercial model quality; needs $2,000+ GPU |

### Understanding the Table

- **Q4 Quantization:** Models are compressed ("quantized") to reduce VRAM requirements. Q4 (4-bit quantization) is the standard balance between quality and size. Higher quantization (Q5, Q6, Q8) preserves more quality but requires more VRAM.
- **"Fits on 16GB GPU?"** indicates whether the model can run entirely in GPU VRAM on the recommended hardware. "Yes (tight)" means it fits but leaves minimal headroom for large context windows.
- **Models that do NOT fit** require either a more expensive GPU (RTX 3090 24GB, RTX 4090 24GB, or professional cards) or will run partially in system RAM at significantly reduced speed.

### Practical Guidance

For the recommended hardware (RTX 4060 Ti 16GB):

| Task Type | Recommended Model | Why |
|-----------|------------------|-----|
| General drafting and summarization | Llama 3.1 8B | Best overall quality at this size |
| Quick, simple tasks | Phi-3 Mini 3.8B | Fast responses, lower quality |
| Reasoning and analysis | DeepSeek-R1 8B or Qwen3-8B | Stronger chain-of-thought capability |
| Multilingual content | Qwen3-8B | Best multilingual support at 8B scale |
| Maximum quality (if VRAM allows) | Llama 3.1 13B | Better than 8B across all tasks; tight fit on 16GB |

For detailed model selection guidance including task-specific recommendations, see [Model Selection](05-model-selection.md). For understanding what these models can and cannot do, see [Understanding LLMs](02-understanding-llms.md).

---

## Honest Cost Comparison

The following comparison uses generic pricing that reflects publicly available information as of early 2025. Actual costs vary by vendor, contract terms, and specific deployment configuration.

### Total Cost of Ownership: Local vs. Cloud

| Deployment Option | Year 1 Cost | Year 2 Cost | Year 3 Cost | 3-Year Total |
|-------------------|-------------|-------------|-------------|-------------|
| **Local workstation (1 user)** | ~$1,700 | ~$200 | ~$200 | ~$2,100 |
| **Local workstation (5 users, shared)** | ~$1,700 | ~$200 | ~$200 | ~$2,100 |
| **Local workstation (10 users, shared)** | ~$1,700 | ~$200 | ~$200 | ~$2,100 |
| **ChatGPT Team (1 user)** | ~$300 | ~$300 | ~$300 | ~$900 |
| **ChatGPT Team (5 users)** | ~$1,500 | ~$1,500 | ~$1,500 | ~$4,500 |
| **ChatGPT Team (10 users)** | ~$3,000 | ~$3,000 | ~$3,000 | ~$9,000 |
| **Cloud AI, govcloud tier (10 users)** | ~$5,000-10,000 | ~$5,000-10,000 | ~$5,000-10,000 | ~$15,000-30,000 |

### Cost Assumptions

| Item | Assumption |
|------|-----------|
| Local Year 1 | $1,500 hardware + $200 electricity/maintenance |
| Local Year 2-3 | $200/year electricity + periodic maintenance |
| ChatGPT Team | ~$25/user/month (publicly listed pricing, subject to change) |
| Cloud govcloud | Variable; includes FedRAMP compliance overhead, enhanced SLAs, dedicated infrastructure |

### Key Insights

1. **For a single user doing non-sensitive work**, a commercial cloud subscription is cheaper than building and maintaining a local workstation. The cost crossover does not occur until Year 3+.

2. **For multi-user teams**, local deployment cost is essentially flat regardless of user count (one workstation serves multiple users), while cloud costs scale linearly per user. The ROI inflection point is around 3-5 users.

3. **For CJI workloads**, the comparison is not straightforward. Cloud options for CJI require FedRAMP High authorization with a CJIS Security Addendum, which significantly increases cost and procurement complexity. Local deployment trades that procurement complexity for security hardening effort. See [CJIS Compliance](09-cjis-compliance.md) for the full hardening framework.

4. **Local deployment has a higher Year 1 cost** due to the hardware capital expenditure. Organizations whose procurement process favors capital expenditure (one-time purchase) over operational expenditure (recurring subscriptions) may find this advantageous.

5. **Cost should not be the sole decision factor.** Data sovereignty, operational independence, and compliance requirements are often more significant drivers than cost alone. See [Why Local LLMs](01-why-local-llms.md) for the complete decision framework.

---

## GPU Alternatives and Considerations

The NVIDIA RTX 4060 Ti 16GB is the recommended GPU, but alternatives exist for different budget levels and deployment scenarios.

### NVIDIA GPU Options

| GPU | VRAM | Approximate Cost | Best For |
|-----|------|-----------------|----------|
| RTX 3060 12GB | 12 GB | $250-350 (used/refurbished) | Budget deployment; runs 7B models, tight for 8B |
| RTX 4060 Ti 16GB | 16 GB | $400-600 | **Recommended**; best price-to-VRAM for this workload |
| RTX 3090 24GB | 24 GB | $700-1,000 (used) | Runs 13B models comfortably; used market value |
| RTX 4090 24GB | 24 GB | $1,600-2,000 | Maximum consumer performance; runs 13B-30B quantized |
| A6000 48GB | 48 GB | $3,000-4,500 | Professional card; runs 70B quantized; multi-user server |

### AMD GPUs (ROCm)

AMD GPUs can run Ollama through the ROCm compute platform. Current status:

| Consideration | Detail |
|---------------|--------|
| **Supported models** | RX 7900 XTX (24GB), RX 7900 XT (20GB), some professional cards |
| **Ecosystem maturity** | Less mature than NVIDIA CUDA; more troubleshooting may be required |
| **Community support** | Smaller community; fewer tutorials and troubleshooting resources |
| **Price-to-VRAM** | Often better than NVIDIA at equivalent VRAM levels |
| **Recommendation** | Viable for experienced Linux users comfortable with additional setup; not recommended as a first deployment |

### Laptop Deployment

Running local LLMs on a laptop is possible with the right hardware:

| Requirement | Specification |
|-------------|--------------|
| **GPU** | NVIDIA laptop GPU with 8GB+ VRAM (e.g., RTX 4060 Mobile, RTX 4070 Mobile) |
| **RAM** | 32GB minimum |
| **Storage** | 512GB+ SSD |
| **Thermal** | Gaming or workstation-class laptop with adequate cooling |
| **Trade-off** | Slower than desktop equivalent due to thermal throttling and mobile GPU limitations |

Laptop deployment is practical for field use, demonstrations, and single-user scenarios. For sustained multi-user workloads, a desktop workstation or server is preferred.

### Server Deployment

For teams of 5+ concurrent users, a server deployment provides better performance and shared access:

| Component | Server Specification | Estimated Cost |
|-----------|---------------------|----------------|
| **GPU** | NVIDIA A6000 48GB or dual RTX 4090 | $3,000-5,000 |
| **RAM** | 64-128GB ECC | $200-500 |
| **CPU** | Server-grade (Xeon or EPYC) | $500-1,500 |
| **Storage** | 2TB+ NVMe RAID | $200-400 |
| **Total** | | $4,000-8,000+ |

Server deployment requires additional infrastructure considerations: rack space, cooling, UPS, and potentially a dedicated VLAN. For multi-user deployment guidance, see [CJIS Compliance](09-cjis-compliance.md) for security architecture and access control requirements.

### Used and Refurbished GPUs

Used GPUs -- particularly the previous-generation RTX 3090 (24GB) -- can significantly reduce costs:

| Consideration | Detail |
|---------------|--------|
| **Cost savings** | 40-60% less than new equivalent |
| **Risk** | No manufacturer warranty; potential for degraded performance from prior mining use |
| **Mitigation** | Purchase from reputable refurbished dealers; stress-test before deployment |
| **Best option** | RTX 3090 24GB used (~$700-1,000) provides 24GB VRAM at near-RTX 4060 Ti 16GB new pricing |
| **Procurement** | Government procurement processes may restrict used/refurbished purchases; verify policies |

---

## Hidden Costs to Budget For

The hardware cost is the most visible line item, but total cost of ownership includes several less obvious expenses. A realistic budget accounts for all of the following.

### Recurring Costs

| Cost Category | Estimated Amount | Frequency | Notes |
|---------------|-----------------|-----------|-------|
| **Electricity** | $15-30/month | Monthly | GPU under load draws 150-300W; actual cost depends on local electricity rates and usage patterns |
| **Internet for updates** | Existing infrastructure | As needed | Model updates are 4-8 GB each; uses existing internet connectivity |
| **Replacement parts** | $50-200/year | Annual estimate | Fans, storage, RAM -- standard workstation maintenance |

### One-Time Setup Costs

| Cost Category | Estimated Amount | Notes |
|---------------|-----------------|-------|
| **IT setup time** | 4-8 hours | Initial installation, configuration, and testing |
| **Security hardening** | 8-24 hours (if CJI workloads) | Implementing the 8 application-layer controls described in [CJIS Compliance](09-cjis-compliance.md) |
| **Network configuration** | 2-4 hours | Firewall rules, VLAN setup, DNS entries |
| **User account setup** | 1-2 hours | If deploying Open WebUI or LiteLLM for multi-user access |

### Ongoing Operational Costs

| Cost Category | Estimated Amount | Frequency | Notes |
|---------------|-----------------|-----------|-------|
| **IT support and troubleshooting** | 2-4 hours/month | Monthly | Model issues, user support, updates |
| **Model updates** | 2-4 hours per update cycle | Quarterly (estimated) | Downloading new model versions, testing, deploying |
| **Air-gapped system updates** | 4-8 hours per update cycle | Quarterly (estimated) | Significantly more effort: download on connected system, transfer via approved media, test, deploy |
| **Analyst training** | 4-8 hours per analyst | Initial onboarding | Expect 2-4 weeks until proficiency; see [Prompting Guide](06-prompting-guide.md) |
| **Ongoing training** | 1-2 hours/quarter per analyst | Quarterly | New models, new prompting techniques, lessons learned |

### Costs Frequently Overlooked

| Commonly Missed Item | Impact |
|---------------------|--------|
| **Analyst ramp-up time** | First 2-4 weeks show reduced productivity as analysts learn prompting; this is an investment, not a loss |
| **Prompt development time** | Creating effective prompt templates for specific workflows takes iteration; budget 10-20 hours for initial template library |
| **Policy development** | Acceptable use policies, review procedures, disclosure requirements -- requires management and legal time |
| **Compliance documentation** | System Security Plan, security baseline documentation, incident response procedures -- required for CJI workloads |
| **Decommissioning** | When hardware reaches end-of-life, secure disposal of GPUs and storage containing model caches must follow agency sanitization procedures |

---

## Procurement Guidance

### Minimum Viable Budget

| Tier | Budget Range | What It Gets |
|------|-------------|-------------|
| **Entry** | $800-1,000 | Used RTX 3060 12GB, 16GB RAM, basic workstation. Runs 7B models. Tight but functional. |
| **Recommended** | $1,000-1,500 | RTX 4060 Ti 16GB, 32GB RAM, NVMe SSD. Runs all 7B-8B models comfortably. Best value. |
| **Enhanced** | $2,000-3,000 | RTX 4090 24GB, 64GB RAM. Runs 13B-30B models. Better quality outputs. |
| **Server** | $4,000-8,000+ | Multi-GPU or professional card. Multi-user deployment. Requires server infrastructure. |

### What to Include in a Budget Request

A complete budget request should itemize:

1. **Hardware** (see specifications above)
2. **Year 1 operational costs** (electricity, IT time, training)
3. **Comparison to cloud alternative** (demonstrates cost-awareness)
4. **Phased deployment plan** (starts with non-CJI pilot -- see [CJIS Compliance](09-cjis-compliance.md))
5. **Expected outcomes** (time savings, capability gains, specific use cases -- see [Use Cases](07-use-cases.md))

For a complete implementation roadmap including phased deployment and compliance milestones, see [Next Steps](12-next-steps.md).

---

## References

- NVIDIA GPU Specifications -- [nvidia.com](https://www.nvidia.com/en-us/geforce/)
- Ollama GPU Compatibility -- [GitHub](https://github.com/ollama/ollama/blob/main/docs/gpu.md)
- Ollama Model Library -- [ollama.com/library](https://ollama.com/library)
- AMD ROCm Platform -- [rocm.docs.amd.com](https://rocm.docs.amd.com/)

---

*This document is part of the [Secure and Affordable In-House AI](../README.md) companion resource. It is an educational resource, not official guidance. Consult your agency's CJIS Systems Officer (CSO) for compliance decisions.*
