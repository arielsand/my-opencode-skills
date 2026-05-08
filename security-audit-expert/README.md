# Security Audit Expert Skill

Performs exhaustive, production-grade security audits of codebases, APIs, frontends, and infrastructure configurations.

## What it does

This skill triggers on any security-related request and produces a dual-output report (Markdown + JSON) with severity ratings, OWASP/CWE references, concrete file paths, and actionable remediation.

### Audit Depths

| Depth | Phases Executed | When to use |
|-------|----------------|-------------|
| `full` (default) | All 11 phases | General security review |
| `quick` | 0–2, 3 surface, 4 surface, 7 surface, 11 | Fast assessment of critical issues |
| `formal` | All 11 + compliance + SAST/DAST | PCI-DSS, GDPR, SOC2 audits |

### 11 Execution Phases

1. **Scope & Reconnaissance** — Map tech stack and entry points
2. **Dependency & Secret Scan** — Find hardcoded credentials, CVE heuristics
3. **Authentication & Authorization** — JWT, sessions, RBAC, password policies
4. **API Security** — Rate limiting, CORS, input validation, mass assignment
5. **Data Protection & Privacy** — PII handling, encryption, IDOR
6. **Input Validation & Injection** — SQLi, XSS, eval, file upload risks
7. **Frontend Security** — localStorage tokens, CSP, CSRF, supply chain
8. **Infrastructure & Configuration** — .env secrets, debug flags, CI/CD
9. **Compliance Mapping** (conditional) — GDPR, SOC2, PCI-DSS controls
10. **SAST / DAST** (conditional) — Static analysis and non-destructive probing
11. **Report Generation** — Dual output (report.md + report.json)

### Generated Report Sections

The report **guarantees** these sections in exact order:

1. Executive Summary — Risk counts, top 3 actions, scope assumptions
2. Methodology — Phases executed, tools, limitations
3. Findings — Critical/High/Medium/Low/Info tables with OWASP + CWE
4. Compliance Matrix (if enabled)
5. SAST/DAST Results (if enabled)
6. Remediation Roadmap — Immediate / Short-term / Long-term
7. Attestation — Disclaimer about automated analysis

### Severity Definitions

| Severity | Description | Fix Timeline |
|----------|-------------|-------------|
| Critical | Remote exploit, full compromise | 24 hours |
| High | Low privilege exploit, significant exposure | 7 days |
| Medium | Specific conditions required | 30 days |
| Low | Defense-in-depth gap | 90 days |
| Info | Best practice, no exploit path | — |

## When to use

- "Audit security of this codebase"
- "Check for vulnerabilities"
- "Scan for secrets"
- "PCI-DSS compliance check"
- "GDPR compliance assessment"
- "SAST/DAST analysis"
- "Security posture review"
- "Check JWT auth"
- "OWASP scan"
- "CWE audit"

## Trigger phrases

The skill triggers on phrases like:
- "audit security"
- "security review"
- "check for vulns"
- "scan for secrets"
- "assess compliance"
- "pentest review"
- "hardening audit"
- "OWASP check"
- "SAST/DAST"
- "security posture"
- Any mention of vulnerabilities, credential leaks, injection flaws, auth weaknesses

## Example

```
Run a full security audit on this project
```

```
Quick security scan of the frontend directory
```

```
Formal PCI-DSS audit of the Payments module
```

```
Check GDPR compliance for user data handling
```

## Constraints

- **Read-only**: Never modifies source code
- **Non-destructive**: No `rm`, `drop`, `migrate:fresh` during audit
- **Safe DAST**: Only GET/HEAD/OPTIONS, no mutations, no brute-force
- **Scope-aware**: Respects `.gitignore`, skips `node_modules/` and `vendor/`
- **Mandatory references**: Every finding must include OWASP Top 10 category AND CWE ID
- **Dual output**: Always produces both `report.md` (human-readable) and `report.json` (machine-readable)

## Compliance Frameworks

When enabled (`formal` depth or explicit request):

| Framework | Controls Mapped |
|-----------|----------------|
| **GDPR** | Art. 25 (Data Protection by Design), Art. 32 (Security), Art. 33 (Breach Notification) |
| **SOC2** | CC6.1–6.3 (Access), CC7.1–7.2 (Security Events & Vulnerability Management) |
| **PCI-DSS** | Req 1–3, 6, 8, 11 (Firewall, Passwords, Cardholder Data, Secure Systems, Auth, Scans) |

## Language

All generated reports are in **English** (security standard), regardless of the user's input language.
