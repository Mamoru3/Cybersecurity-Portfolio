# <LAB / MACHINE NAME> - Sanitized Writeup

- **Difficulty:** (Easy / Medium / Hard) - of course discard this for real writeups
- **OS:** (Linux / Windows)  
- **Date:** day-month-year

---

## 1) Overview
**Goal:** (What "success" means for this lab)  
**Result:** (e.g., user access achieved, root achieved, objectives completed)  
**Key skills practiced:** (3–6 bullets)
- 
- 
- 

**High-level attack path (1–3 lines):**  
Recon -> Initial access -> Privilege escalation-> Post-exploitation (optional)

---

## 2) Environment & Scope (Sanitized)
- This was performed in a legal lab environment for learning.
- Steps are described at a **high level** to focus on methodology and remediation.

**Target summary (sanitized):**
- **IP/Host:** `<redacted>`  
- **Exposed services:** (e.g., HTTP, SMB, SSH)  
- **Notable versions/config:** (only if it’s safe/general)

---

## 3) Recon & Enumeration
### What I checked first
- (Ports/services discovered and why they mattered)
- (Web content discovery, directory checks, auth surfaces, SMB shares, etc.)

### Key findings (high level)
- **Finding A:** (what you learned)
- **Finding B:** (what you learned)
- **Finding C:** (what you learned)

**Evidence (sanitized):**
- Screenshot(s) or short snippets with redactions  
  - `evidence/recon-01.png`
  - `evidence/service-banner.txt`

---

## 4) Initial Access (High Level)
### Entry point category
Choose one:
- Misconfiguration / weak permissions
- Credential issue (reuse, weak, exposed)
- Web vulnerability (auth flaw, injection class, file inclusion, SSTI/SSRF, etc.)
- AD / Kerberos weakness
- Service exploitation (high-level only)

### Reasoning
- Why this was the most likely path (1–5 bullets)

### Outcome
- What access you achieved (e.g., low-priv shell, user account)  
- Constraints/issues encountered (if any)

**Evidence (sanitized):**
- `evidence/access-01.png`

---

## 5) Privilege Escalation (High Level)
### Privesc vector category
Choose one:
- Sudo/permissions misconfig
- Insecure service / scheduled task
- Credential discovery (configs, history, memory-high-level)
- Kernel / vulnerable component (high-level)
- AD escalation path (groups, delegation, ACLs-high-level)

### Reasoning & checks performed
- What you checked and why (bullets)

### Outcome
- Privilege achieved (e.g., local admin / root)
- What confirmed success (sanitized)

**Evidence (sanitized):**
- `evidence/privesc-01.png`

---

## 6) Post-Exploitation (Optional, Keep Minimal)
> Only include what’s relevant to learning and defense.

- What you validated (e.g., proof of access, basic situational awareness)
- Any cleanup you performed (if applicable)

---

## 7) Remediation & Defense Notes
### Fix recommendations (actionable)
- **Fix 1:** (specific, practical)
- **Fix 2:**  
- **Fix 3:**  

### Detection ideas (if SOC/blue-team relevant)
- Logs/telemetry to enable (e.g., Windows Event IDs, web logs, auth logs)
- Suspicious behaviors to alert on (high-level)
- Suggested control improvements (least privilege, patching, secrets mgmt)

### Mapping (Optional)
- **MITRE ATT&CK:** (Tactic/Technique names)
- **OWASP:** (Axx category if web-related)

---

## 8) Lessons Learned
- What I learned:
- What I'd do differently next time:
- 1–2 resources I used (docs, articles, etc.):

---

## 9) Appendix (Sanitized)
**Tools used:** (general list)  
**Commands:** keep minimal, non-sensitive, and avoid exploit-ready copy/paste.  
**Artifacts:** (if you produced anything safe to share, e.g., a report PDF)
