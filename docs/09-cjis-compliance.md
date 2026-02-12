# CJIS Security Policy Compliance for Local LLMs

This is the most critical section in this guide. If you deploy a local LLM and process Criminal Justice Information (CJI), your deployment must comply with the FBI CJIS Security Policy. This document provides a complete compliance framework -- what Ollama provides by default, where the gaps are, and how to close them.

**Important:** This analysis is based on publicly available CJIS Security Policy v6.0 (October 2023) documentation. Consult your agency's CJIS Systems Officer (CSO) for compliance decisions specific to your environment. This guide is educational, not legal or compliance advice.

---

## Why CJIS Compliance Matters for Local LLMs

A common misconception is that running AI locally, especially on an air-gapped network, automatically ensures compliance. It does not.

Air-gap capability addresses **one piece** of CJIS compliance: data transmission. But CJIS has **13 policy areas** covering authentication, access control, audit trails, encryption, incident response, and more. Keeping data on your network is necessary. It is not sufficient.

The presentation states that "air-gap capability ensures compliance with strict security protocols." The accurate statement is: **air-gap capability addresses the data transmission requirement (Policy Area 5.10 in part) while the remaining 12 policy areas require additional controls.**

Your agency's CJIS Systems Officer (CSO) will ask: "How does this system meet each CJIS policy area?" This document provides the framework to answer that question.

---

## CJIS Security Policy v6.0 -- Full 13-Area Compliance Mapping

The following table evaluates default Ollama against all 13 CJIS Security Policy areas and projects the compliance status after the hardening described in this guide.

| # | CJIS Policy Area | Requirement Summary | Default Ollama | With Hardening |
|---|-------------------|---------------------|----------------|----------------|
| 5.1 | Information Exchange | Documented sharing agreements | N/A (agency policy) | N/A (agency policy) |
| 5.2 | Incident Response | Detection + 1-hour breach notification | NOT COMPLIANT | COMPLIANT |
| 5.3 | Physical Security | Facility access controls | COMPLIANT (existing) | COMPLIANT (existing) |
| 5.4 | Personnel Security | Background checks + least privilege | PARTIAL | COMPLIANT |
| 5.6 | Authentication | MFA mandatory (Oct 2024 mandate) | NOT COMPLIANT | COMPLIANT |
| 5.7 | Audit Logging | All CJI access logged, 1-year retention | NOT COMPLIANT | COMPLIANT |
| 5.9 | Access Control | RBAC + least privilege | NOT COMPLIANT | COMPLIANT |
| 5.10 | Encryption | FIPS 140-2, TLS 1.2+, AES-256 | NOT COMPLIANT | COMPLIANT |
| 5.11 | Media Protection | Secure disposal + sanitization | PARTIAL | COMPLIANT |
| 5.12 | Remote Access | Encryption + auth for remote sessions | COMPLIANT (existing) | COMPLIANT (existing) |
| 5.13 | Configuration Mgmt | Documented security baselines | PARTIAL | COMPLIANT |
| 5.14 | Security Assessment | Audit authorization | NOT COMPLIANT | COMPLIANT |
| -- | Training & Awareness | Annual CJIS training | N/A (agency responsibility) | Enhanced |

**Bottom line: Default Ollama satisfies approximately 5 of 13 CJIS policy areas. With deliberate hardening, all 13 areas are addressable. The gap is "hardening required," not "fundamentally incompatible."**

---

## The Infrastructure vs. Application Security Distinction

This is the most important conceptual distinction for understanding why a secure network alone is not enough.

> "A bank vault with biometric access containing unlocked filing cabinets. The vault security does not make the filing cabinets secure."

Most law enforcement agencies have invested heavily in infrastructure security -- and that investment is real and valuable. However, CJIS policy applies where CJI is **processed**, not just where it is stored or transmitted. Ollama processes CJI at the application layer, which is where the gaps exist.

```
INFRASTRUCTURE LAYER (What your agency likely already provides)
  [OK] Network segmentation with controlled gateway
  [OK] Active Directory + MFA (operating system level)
  [OK] SIEM logging (operating system events)
  [OK] Full-disk encryption (FIPS 140-2 validated)
  [OK] Physical facility security (badge access, surveillance)

  --- Security controls END here ---

APPLICATION LAYER (What Ollama needs but does not have by default)
  [GAP] Ollama API authentication
  [GAP] Prompt/response audit logging
  [GAP] Role-based access control
  [GAP] API encryption (TLS)
  [GAP] Cache file encryption
  [GAP] Incident detection for AI-specific threats
  [GAP] Configuration security baseline
  [GAP] Cache file sanitization procedures

  CJI IS PROCESSED HERE ^
```

**Your infrastructure security is excellent. It is also insufficient for CJIS compliance at the application layer.** This guide addresses the 8 application-layer gaps that bridge the distance between your existing security posture and full CJIS compliance.

---

## The 8 Application-Layer Gaps

Each gap below follows the same structure: what CJIS requires, what Ollama provides by default, why existing infrastructure does not close the gap, and the remediation direction.

These gaps were identified through technical assessment of Ollama's default configuration against CJIS Security Policy v6.0 requirements. They apply to any agency deploying Ollama for CJI workloads.

---

### Gap 1: Authentication (CJIS 5.6)

**What CJIS requires:** All users accessing criminal justice information systems must be uniquely identified and authenticated. Multi-Factor Authentication (MFA) has been mandatory since October 1, 2024 (CJIS 5.6.2.2).

**What Ollama provides by default:** No authentication whatsoever. The Ollama API accepts requests from any source on the network without verifying user identity. Anyone who can reach the API endpoint can query the model.

**Why existing infrastructure does not fix this:** Active Directory authenticates the user to the operating system, not to the Ollama application. Once a user logs into the workstation, the Ollama API is unrestricted. The SIEM cannot determine which user sent which prompt because Ollama does not track user identity.

**Remediation direction:**
- Deploy a reverse proxy (Nginx or Caddy) in front of the Ollama API
- Integrate with Active Directory for user identity verification and session token management
- Pass authenticated user identity to the logging layer for per-user audit trails
- Inherit MFA enforcement from existing AD+MFA infrastructure by requiring re-authentication at the application layer
- Consider LiteLLM proxy as an all-in-one solution for auth + RBAC + logging

---

### Gap 2: Authorization and Role-Based Access Control (CJIS 5.9)

**What CJIS requires:** Access to CJI must be restricted to authorized users based on need-to-know and least privilege (CJIS 5.9.1). Different personnel should have different levels of access based on their role and authorization.

**What Ollama provides by default:** No access controls. All users who can reach the API have identical, unrestricted access to all models and all capabilities. There is no distinction between an administrative assistant authorized for public records and a detective authorized for investigative files.

**Why existing infrastructure does not fix this:** AD group-based file permissions control operating system access, not Ollama API access. An analyst with "public records only" clearance and a detective with "full CJI access" currently have identical Ollama access.

**Remediation direction:**
- Deploy RBAC middleware (LiteLLM supports this) mapping Active Directory security groups to Ollama model and data permissions
- Define an access matrix specifying which AD groups can access which models and which data classifications
- Implement prompt content filtering to block unauthorized data-level queries
- Enforce need-to-know at the application layer, not just the network layer

---

### Gap 3: Audit Logging (CJIS 5.7)

**What CJIS requires:** All access to CJI must be logged. Audit logs must be protected from unauthorized modification and retained for a minimum of one year (CJIS 5.7.1). Logs must support forensic reconstruction of who accessed what CJI and when.

**What Ollama provides by default:** No prompt or response logging. The SIEM records that the Ollama process started and that HTTP requests occurred, but captures zero information about what CJI was accessed, by whom, or what the model returned.

**Why existing infrastructure does not fix this:** The SIEM logs operating system events (process execution, file access, network connections), not application-layer data. If an insider queries 500 case files via Ollama over 3 hours, the SIEM shows "500 HTTP requests" but provides zero evidence of which CJI was accessed. Forensic investigation cannot reconstruct the scope of a potential breach, making CJIS-mandated breach notification impossible to scope accurately.

**Remediation direction:**
- Deploy logging middleware that captures all prompts and responses in structured format
- Enrich logs with CJI-specific tagging (detected case numbers, SSNs, and identifiers)
- Forward structured logs to the existing SIEM with 1-year retention enforcement
- Ensure logs are tamper-resistant and access-controlled
- LiteLLM provides built-in prompt/response logging with database storage

---

### Gap 4: Encryption in Transit (CJIS 5.10)

**What CJIS requires:** CJI must be encrypted in transit using FIPS 140-2 validated cryptographic modules and TLS 1.2 or higher (CJIS 5.10.1.2).

**What Ollama provides by default:** HTTP only. All prompts and responses are transmitted in plaintext, even on localhost. Network packet capture tools can reveal CJI-containing prompts in cleartext on the local network segment.

**Why existing infrastructure does not fix this:** Full-disk encryption (FDE) protects data on a powered-off disk. It does not encrypt network traffic between processes on a running system. A packet capture on the local network reveals everything.

**Remediation direction:**
- Deploy a TLS reverse proxy with FIPS 140-2 validated OpenSSL modules
- All communications between user-facing components and the Ollama runtime must be encrypted via TLS 1.2 or higher
- For CJI workloads, consider mutual TLS (mTLS) where both client and server authenticate via certificates
- This is one of the simpler gaps to close -- standard TLS termination proxy configuration

---

### Gap 5: Encryption at Rest -- Application Layer (CJIS 5.10)

**What CJIS requires:** CJI must be encrypted at rest using FIPS 140-2 validated modules (CJIS 5.10.1.1).

**What Ollama provides by default:** Ollama writes KV cache files to temporary directories (typically `/tmp/ollama-*/kvcache`) in plaintext. These cache files persist across prompts and can contain context from multiple queries, including any CJI that was processed. Full-disk encryption does not help because the filesystem is mounted and decrypted during operation -- any process running on the system can read the cache files.

**Why existing infrastructure does not fix this:** Full-disk encryption protects data when the system is powered off. While Ollama is running, the disk is mounted and decrypted. Cache files containing CJI are readable by any process with filesystem access.

**Remediation direction:**
- Deploy encrypted filesystem volumes (dm-crypt/LUKS on Linux, BitLocker volumes on Windows) specifically for all Ollama data directories
- This provides application-layer encryption independent of and in addition to full-disk encryption
- Configure Ollama to write all cache, model, and temporary files to the encrypted volume
- Ensure encryption keys are managed separately from the data they protect

---

### Gap 6: Incident Response (CJIS 5.2)

**What CJIS requires:** Organizations must have the ability to detect security incidents involving CJI and provide breach notification within one hour (CJIS 5.2.1).

**What Ollama provides by default:** No anomaly detection, no alerting, and no forensic capability specific to AI workloads. An insider running a script to query thousands of records via the Ollama API over several hours would appear as normal HTTP traffic to the SIEM. The breach would likely be discovered days or weeks later during an unrelated audit, far exceeding the one-hour notification window.

**Why existing infrastructure does not fix this:** Standard SIEM rules are designed for known attack patterns (failed logins, port scans, malware signatures). AI-specific abuse patterns -- bulk CJI extraction via sequential prompts, prompt injection attacks, model manipulation -- are not covered by standard detection rules.

**Remediation direction:**
- Develop Ollama-specific SIEM correlation rules targeting AI abuse patterns
- Deploy anomaly detection for prompt patterns (bulk queries, unusual data access patterns, off-hours usage spikes)
- Create an Ollama-specific incident response playbook
- Conduct tabletop exercises to validate detection and response procedures
- Define alert thresholds (e.g., more than N CJI-containing queries within M minutes triggers an alert)

---

### Gap 7: Configuration Management (CJIS 5.13)

**What CJIS requires:** Systems processing CJI must have documented security configuration baselines (CJIS 5.13). Organizations must be able to demonstrate that security configurations are defined, applied, and verified.

**What Ollama provides by default:** No security configuration baseline. Your organization likely has OS hardening baselines (STIG/CIS benchmarks) but no Ollama-specific baseline addressing secure API binding, cache file permissions, port restrictions, model selection policy, or version control and patching procedures.

**Why existing infrastructure does not fix this:** OS-level hardening guides do not cover application-specific configurations. Ollama has its own set of security-relevant settings that are not addressed by any standard benchmark.

**Remediation direction:**
- Develop an Ollama security hardening guide following the CIS benchmark structure
- Document configuration baselines covering:
  - API binding (localhost only -- `127.0.0.1`, never `0.0.0.0`)
  - Cache directory permissions (restrictive, application-user only)
  - Port configuration and firewall rules
  - Model whitelist policy (approved models only)
  - Version control and patching schedule
  - Logging configuration
- Create a compliance verification checklist for periodic review

---

### Gap 8: Media Protection -- Cache Sanitization (CJIS 5.11)

**What CJIS requires:** Media containing CJI must be protected, sanitized, and securely disposed of (CJIS 5.11). This includes electronic media, storage devices, and any files containing CJI.

**What Ollama provides by default:** No documented sanitization procedure for cache files containing CJI. No secure deletion of model context between cases or sessions. Workstation decommissioning procedures likely do not address Ollama-specific files and directories because they were written before Ollama existed.

**Why existing infrastructure does not fix this:** Standard decommissioning procedures address drives, RAM, and removable media. They do not address Ollama's application-specific cache directories, model files that may contain fine-tuned CJI data, or conversation history stored by front-end tools like Open WebUI.

**Remediation direction:**
- Develop secure deletion procedures for Ollama cache files using cryptographic erasure (not simple file deletion)
- Create a media sanitization checklist specific to Ollama directories and associated tools
- Document retention and disposal procedures for:
  - KV cache files
  - Model files (especially any fine-tuned on agency data)
  - Open WebUI conversation databases
  - Audit log archives after retention period
- Integrate Ollama sanitization into existing workstation decommissioning procedures

---

## Phased Compliance Approach

CJIS compliance for a local LLM is not an all-or-nothing proposition. A phased approach manages risk while building capability. The critical principle: **CJI is never processed on an unhardened system.**

### Phase 1: Non-CJI Pilot

**Objective:** Prove the technology works. Build organizational confidence. Zero CJI risk.

- Deploy Ollama with a user interface (Open WebUI) for non-CJI workloads only
- Use cases: report template generation, training scenarios with synthetic data, public records analysis, administrative drafting, policy research
- Deploy Data Loss Prevention (DLP) to prevent accidental CJI input (see next section)
- 5-10 pilot users, 4-week evaluation period
- **Risk level: LOW** -- no CJI data involved, failure has zero compliance implications

**Go/No-Go Gate:** Steering committee reviews pilot results before proceeding.

### Phase 2: Security Hardening

**Objective:** Implement all 8 application-layer CJIS controls.

- Authentication middleware with AD integration
- RBAC layer mapping AD groups to permissions
- Full prompt/response audit logging with SIEM integration
- TLS encryption for all API communications
- Encrypted cache directories
- Security configuration baseline documentation
- Cache sanitization procedures
- Personnel security controls at the application layer

**Go/No-Go Gate:** Security team validates all 8 controls before proceeding.

### Phase 3: Compliance Validation

**Objective:** Validate all controls and achieve Authorization to Operate (ATO).

- Internal security assessment against all 13 CJIS areas
- Third-party penetration test
- Incident response plan development and tabletop exercise
- System Security Plan (SSP) documentation
- Plan of Action and Milestones (POA&M) for any residual findings
- ATO recommendation to leadership

**Go/No-Go Gate:** ATO must be signed before any CJI data is processed.

### Phase 4: CJI Production

**Objective:** Begin processing CJI workloads with full controls in place.

- Limited initial deployment: 2-3 approved use cases
- Expanded user base
- Continuous monitoring and alerting from day one
- Monthly security reviews for the first 6 months
- CJI workloads proceed ONLY after ATO is signed

### Phase 5: Scale and Optimize

- Expand to all approved use cases
- Scale to full analyst workforce
- Advanced capabilities: RAG integration, custom model configurations
- Continuous improvement based on user feedback

```
Phase Timeline (Approximate)

Month:  1    2    3    4    5    6    7    8    9    10   11   12
        |----|----|----|----|----|----|----|----|----|----|----|---->

Phase 1:|####|
        Non-CJI Pilot (zero CJI risk)
             |---- GO/NO-GO ----|
Phase 2:     |##########################|
             Security Hardening
                                        |---- GO/NO-GO ----|
Phase 3:                                |###########|
                                        Compliance Validation
                                                    |---- GO/NO-GO
Phase 4:                                             |#########|
                                                     CJI Production
Phase 5:                                                        |--->
                                                                Scale
```

---

## Data Loss Prevention for the Non-CJI Phase

During Phase 1 (non-CJI pilot), the critical question is: **What prevents users from accidentally or intentionally inputting CJI into a system that is not yet hardened for CJI?**

The answer should not be "trust users not to." The answer should be technical enforcement.

**Microsoft Presidio** is an open-source DLP engine (Apache 2.0 license) that can be integrated into the API proxy layer (e.g., LiteLLM) to scan every prompt before it reaches Ollama.

### What Presidio Provides

- Detection of 180+ Personally Identifiable Information (PII) entity types including Social Security numbers, dates of birth, driver's license numbers, and passport numbers
- Combined regex and NLP analysis for high-accuracy detection with contextual understanding
- Customizable recognizers for agency-specific patterns (case number formats, badge IDs, NCIC numbers, internal identifiers)
- Configurable confidence thresholds:
  - **Block** (high confidence, score 0.75+): Prompt is rejected before reaching Ollama
  - **Warn** (medium confidence, score 0.50-0.74): User is warned, prompt proceeds with logging
  - **Ignore** (low confidence, below 0.50): No action
- Complete audit trail for all violations forwarded to SIEM

### Common Evasion Vectors to Test

Before trusting your DLP layer, test it against common evasion techniques:

1. **Character substitution:** Replacing digits with words ("five five five" instead of "555")
2. **Unicode homoglyphs:** Visually similar characters from different Unicode blocks
3. **Base64 encoding:** Encoding CJI data before submitting
4. **Prompt injection:** Instructing the model to ignore DLP rules
5. **Chunking across messages:** Splitting a SSN across multiple prompts
6. **Bulk extraction:** Requesting "all records matching..." patterns
7. **File upload bypass:** Embedding CJI in uploaded documents

Your DLP configuration should be validated against these vectors before the pilot launches. Document the test results and any mitigations applied.

### Key DLP Takeaway

DLP transforms the non-CJI pilot from "we trust users not to input CJI" to "we enforce at the infrastructure level that CJI cannot reach the system." This is a fundamentally different risk posture and is critical for gaining security team approval for Phase 1.

---

## Known Vulnerabilities -- CVE Awareness

Transparency about known vulnerabilities builds trust faster than discovery during an audit. The following CVEs have been publicly disclosed for Ollama. Each has a documented mitigation within a standard hardening architecture.

| CVE | Severity | Description | Mitigation |
|-----|----------|-------------|------------|
| CVE-2025-63389 | CRITICAL (9.1) | Authentication bypass allows unauthenticated API access | Auth middleware eliminates exposure; reverse proxy enforces authentication before requests reach Ollama |
| CVE-2025-1975 | HIGH | Denial of Service via crafted API requests | Rate limiting in reverse proxy + resource monitoring + alerting |
| CVE-2025-51471 | HIGH | Authentication token theft via malicious model server | Validate all model transfers through approved channels; no direct connection to external model servers on air-gapped systems |
| CVE-2024-39719 | HIGH (7.5) | Remote Code Execution via malicious model file parsing | Model integrity verification + content inspection before model loading |
| CNVD-2025-04094 | HIGH | Unauthorized access via default 0.0.0.0 network binding | Localhost-only binding (127.0.0.1) + firewall rules blocking external access |

### Why Disclosing Vulnerabilities Matters

- Active CVE disclosure by the Ollama project indicates a security maturity trajectory -- vulnerabilities are identified, published, and patched rather than hidden
- Each vulnerability has a documented mitigation within a standard hardening architecture
- A phased approach ensures no CJI is exposed before mitigations are validated
- Your security team can independently verify each CVE and its corresponding mitigation
- Proactive disclosure in your ATO documentation demonstrates thorough research

### Patching on Air-Gapped Systems

Updating Ollama on an air-gapped or network-segmented system requires a defined process:

1. Monitor Ollama releases and CVE disclosures on a connected system
2. Download updated binaries and model files on the connected system
3. Transfer via your agency's approved Cross-Domain Solution (CDS) or approved media transfer process
4. Validate file integrity (checksums) before installation
5. Test in a non-production environment before deploying to the production system
6. Document the update in your configuration management log

Establish a batch update schedule aligned with your CDS transfer windows. Do not let patching fall behind because the transfer process is inconvenient.

---

## Key CJIS Compliance Takeaways

1. **Air-gap is necessary but not sufficient.** It addresses data transmission. CJIS has 12 other policy areas.

2. **Default Ollama is not CJIS compliant.** No open-source LLM runtime ships with CJIS compliance. The question is not "which one is compliant" but "which one is easiest to harden."

3. **The gap is bridgeable.** All 8 application-layer gaps have documented remediation paths using standard, well-understood security engineering.

4. **Infrastructure security is not application security.** Your network, AD, SIEM, and FDE are excellent. They do not protect the application layer where CJI is processed.

5. **Phase your deployment.** Start with non-CJI workloads (zero compliance risk), harden, validate, then deploy CJI workloads. Never process CJI on an unhardened system.

6. **DLP is your Phase 1 safety net.** Technical enforcement preventing CJI input is fundamentally different from trusting users not to input CJI.

7. **Proactive transparency builds trust.** Disclose gaps and vulnerabilities before your security team finds them. It demonstrates that you understand the compliance landscape.

8. **Consult your CSO.** This guide provides a framework. Your agency's CJIS Systems Officer makes the compliance decisions. Engage them early, not after you have built something.

---

## References

- [FBI CJIS Security Policy v6.0](https://www.fbi.gov/services/cjis/cjis-security-policy-resource-center) (October 2023)
- [CJIS Security Policy Resource Center](https://www.fbi.gov/services/cjis/cjis-security-policy-resource-center/information-agreements)
- [Ollama GitHub Repository](https://github.com/ollama/ollama)
- [Microsoft Presidio (Open Source DLP)](https://github.com/microsoft/presidio)
- [LiteLLM Proxy Documentation](https://docs.litellm.ai/)
- [Open WebUI Project](https://github.com/open-webui/open-webui)
- [NIST SP 800-53 Rev 5](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final) -- Security and Privacy Controls
- [FedRAMP High Baseline](https://www.fedramp.gov/baselines/)

---

*This document is part of the [Secure and Affordable In-House AI](../README.md) guide. It is educational, not legal or compliance advice. Consult your agency's CJIS Systems Officer for compliance decisions.*
