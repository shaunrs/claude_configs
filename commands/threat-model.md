---
description: Complete a security Threat Model of the codebase
---

OBJECTIVE:
You are a senior security engineer conducting a focused, comprehensive, auditor-ready threat model document for $ARGUMENTS


METHODOLOGY:

**Primary: STRIDE + DREAD-D**
- STRIDE for systematic threat identification (Microsoft-developed, industry standard)
- DREAD-D for risk scoring (omits Discoverability to avoid "security through obscurity")
- Format follows https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html recommendations

**Optional Extension: LINDDUN for Privacy**
When the system handles personal data or privacy is a core concern, layer LINDDUN analysis:
- **L**inkability - Can actions/data be correlated to one person?
- **I**dentifiability - Can identity be revealed from data?
- **N**on-repudiation - Can someone's actions be proven? (Note: opposite goal to STRIDE's Repudiation)
- **D**etectability - Can existence of data be determined?
- **D**isclosure - Can information be exposed?
- **U**nawareness - Does user know what happens to their data?
- **N**on-compliance - Are regulations being violated?

Reference: https://linddun.org/ (included in ISO 27550)

**Methodology Context (2024/2025):**
- Microsoft discontinued DREAD internally due to subjectivity, replacing it with the "Bug Bar" system
- However, DREAD-D remains widely used by Fortune 500, military, and security practitioners
- STRIDE + DREAD-D is a valid, recognized approach when scoring is applied consistently
- Alternative scoring systems include OWASP Risk Rating (Likelihood × Impact) and CVSS 4.0

CRITICAL INSTRUCTIONS:
1. MINIMIZE FALSE POSITIVES: Only flag issues where you're >80% confident of actual exploitability
2. AVOID NOISE: Skip theoretical issues, style concerns, or low-impact findings
3. FOCUS ON IMPACT: Prioritize vulnerabilities that could lead to unauthorized access, data breaches, or system compromise
4. VALIDATE THE IMPLEMENTATION: It's ok to read CLAUDE.md, READMEs and PRD documents - but they are often outdated and incomplete. **Always validate what happens in code**

DOCUMENT STRUCTURE:
1. Document Metadata
   - Version, owner, reviewers, last updated
   - Scope and out-of-scope definitions

2. System Overview
   - Architecture description
   - Data Flow Diagram (Mermaid)
   - Trust boundaries
   - Assets requiring protection
   - Entry/exit points

3. Threat Analysis (STRIDE)
   For each component:
   - Spoofing threats
   - Tampering threats
   - Repudiation threats
   - Information Disclosure threats
   - Denial of Service threats
   - Elevation of Privilege threats

4. Privacy Analysis (LINDDUN) - Optional
   When privacy is a core concern, analyze:
   - Linkability threats (correlating user actions)
   - Identifiability threats (de-anonymization risks)
   - Non-repudiation threats (unwanted accountability)
   - Detectability threats (metadata leakage)
   - Disclosure threats (data exposure)
   - Unawareness threats (hidden data flows)
   - Non-compliance threats (regulatory violations)

5. Risk Assessment (DREAD-D)

   **Per-Factor Scoring Guide** (rate each D, R, E, A factor 1-10):

   | Score | Damage | Reproducibility | Exploitability | Affected Users |
   |-------|--------|-----------------|----------------|----------------|
   | **10** | Complete key compromise | Trivial, automated | Script kiddie | All users |
   | **7-9** | Significant data exposure | Reliable, few steps | Moderate skill | Most users |
   | **4-6** | Limited data exposure | Sometimes works | Expert required | Some users |
   | **1-3** | Minimal impact | Rare, complex | Nation-state | Few users |

   **Severity Thresholds** (based on average of 4 factors):

   Default thresholds follow EC-Council standards (conservative):

   | Average Score | Severity | Priority |
   |---------------|----------|----------|
   | **≥8.0** | Critical | Immediate action required |
   | **5.0–7.9** | High | Address before release |
   | **2.5–4.9** | Medium | Address in normal cycle |
   | **<2.5** | Low | Accept or address opportunistically |

   **Important:** If the project has an existing threat model with established thresholds, use those instead to maintain consistency. Common alternative thresholds include:
   - High ≥6.0 (less conservative, aligns with per-factor "4-6 = medium" guidance)
   - High ≥7.0 (permissive, fewer items flagged as High)

   **Exception:** Threats causing permanent, irrecoverable data loss for all users may be elevated to Critical regardless of score.

6. Mitigations
   For each threat:
   - Current mitigation (if any)
   - Status: Mitigated | Accepted | Transferred | Open
   - Residual risk

7. Platform/Environment Threats (Out of Control)
   Document browser/OS-level threats as "Accepted" with rationale:
   - Memory swap to disk
   - Malware on device
   - Malicious browser extensions
   - JavaScript runtime limitations
   - Server-delivered code trust model

8. Assumptions & Dependencies
   - External dependencies
   - Trust assumptions

9. References
   - Links to sequence diagrams, code

SECURITY CATEGORIES TO EXAMINE:

**Input Validation Vulnerabilities:**
- SQL injection via unsanitized user input
- Command injection in system calls or subprocesses
- XXE injection in XML parsing
- Template injection in templating engines
- NoSQL injection in database queries
- Path traversal in file operations

**Authentication & Authorization Issues:**
- Authentication bypass logic
- Privilege escalation paths
- Session management flaws
- JWT token vulnerabilities
- Authorization logic bypasses

**Crypto & Secrets Management:**
- Hardcoded API keys, passwords, or tokens
- Weak cryptographic algorithms or implementations
- Improper key storage or management
- Cryptographic randomness issues
- Certificate validation bypasses
- Keying material in memory (see special handling below)

**Injection & Code Execution:**
- Remote code execution via deseralization
- Pickle injection in Python
- YAML deserialization vulnerabilities
- Eval injection in dynamic code execution
- XSS vulnerabilities in web applications (reflected, stored, DOM-based)

**Data Exposure:**
- Sensitive data logging or storage
- PII handling violations
- API endpoint data leakage
- Debug information exposure

Additional notes:
- Even if something is only exploitable from the local network, it can still be a HIGH severity issue

**KEYING MATERIAL IN MEMORY - Special Handling:**

For threats involving cryptographic keys, passwords, or other sensitive material persisting in memory:

1. **NEVER mark as "Accepted" without best-effort zeroization**
   - Check if the codebase has a memory zeroization function (e.g., `zeroKey()`, `SecureZeroMemory`, `explicit_bzero`)
   - If such a function exists, verify it is being called on the sensitive material after use

2. **Correct status assignment:**
   - **Mitigated**: Zeroization IS being applied (e.g., `zeroKey(keyBytes)` called after use)
     - Residual risk: "Medium - Language runtime may retain copies" (JS GC, Python GC, etc.)
   - **Partial**: Zeroization SHOULD be applied but currently ISN'T
     - Add a TODO recommendation to apply zeroization
   - **Open**: No zeroization mechanism exists in the codebase yet
     - Recommend implementing a zeroization utility

3. **Platform limitations are residual risk, not acceptance:**
   - The inherent limitation that garbage collectors may retain memory copies is documented as *residual risk* on a "Mitigated" item
   - It is NOT a reason to skip implementing best-effort zeroization
   - Example: "Mitigated | Medium - JS GC may retain copies" (correct)
   - NOT: "Accepted | Inherent JS limitation" (incorrect if zeroization not implemented)

4. **Items to check for zeroization:**
   - Master keys, encryption keys, derived keys
   - Passwords and password-derived values
   - Secret keys, API keys, tokens
   - Session keys, bundle keys
   - Any intermediate cryptographic material

ANALYSIS METHODOLOGY:

Phase 1 - Repository Context Research (Use file search tools):
- Identify existing security frameworks and libraries in use
- Look for established secure coding patterns in the codebase
- Examine existing sanitization and validation patterns
- Understand the project's security model and threat model
- **Search for memory zeroization functions** (e.g., `zeroKey`, `zeroize`, `SecureZeroMemory`, `explicit_bzero`)

Phase 2 - Comparative Analysis:
- Identify deviations from established secure practices
- Look for inconsistent security implementations
- Flag code that introduces attack surfaces

Phase 3 - Vulnerability Assessment:
- Examine each modified file for threats
- Trace data flow from user inputs to sensitive operations
- Look for privilege boundaries being crossed unsafely
- Identify injection points and unsafe deserialization

## Output Files

### Primary: Threat Model Document
- Output a single file with all sections in Markdown format documenting the threat model
- Will be extensible for future system components
- **The owner of the threat model will be Shaun S**

### Secondary: Risk & Control Summary (Sidecar)
After completing the threat model, generate a **sidecar summary file** named `threat-model-summary.md` in the same directory.

This summary is formatted for **ISAE 3000-style assurance audits** and should be written in plain English for non-technical reviewers.

**Required sections:**

1. **Overview** - Brief description of the system and its main components

2. **Risk & Control Register** - Tables organized by severity (Critical, High, then topical groupings):

   | Risk ID | Risk Description | Control(s) in Place | Status |
   |---------|-----------------|---------------------|--------|
   | ID from threat model | Plain English description of what could go wrong | What safeguards exist | **Mitigated** / **Partial** / **Accepted** / **Open** |

   Guidelines:
   - Use plain English, avoid jargon (e.g., "encryption key remains in memory" not "MK in JS heap")
   - Include residual risk in parentheses where relevant
   - Group related risks (OAuth, Enclave, Gateway) into subsections

3. **Platform/Environment Risks** - Separate table for risks outside application control with acceptance rationale

4. **Open Action Items** - Table of unresolved items requiring attention:

   | Priority | Recommendation | Threat Addressed |
   |----------|---------------|------------------|

5. **Summary Statistics** - Count of threats by severity and status distribution

**Formatting requirements:**
- Use horizontal rules (`---`) between major sections
- Bold all status values
- Keep risk descriptions to one sentence where possible
- Ensure the document is scannable by auditors unfamiliar with the codebase
