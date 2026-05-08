---
name: security-audit-expert
description: |
  MUST use whenever the user asks for a security audit, vulnerability assessment,
  penetration test review, security scan, compliance check (GDPR, SOC2, PCI-DSS),
  or hardening review of any codebase, API, frontend, or infrastructure.
  Triggers on phrases like: "audit security", "security review", "check for vulns",
  "scan for secrets", "assess compliance", "pentest review", "hardening audit",
  "OWASP check", "CWE audit", "SAST/DAST", "security posture", or any request
  that involves finding vulnerabilities, misconfigurations, credential leaks,
  injection flaws, auth weaknesses, or data-exposure risks in code.
  Use this skill even if the user only mentions a subset (e.g., "check JWT auth")
  — the skill will scope appropriately.
---

# Security Audit Expert

Perform exhaustive, production-grade security audits of codebases, APIs,
frontends, and infrastructure configurations. The audit produces a dual-output
report (Markdown + JSON) with severity ratings, OWASP/CWE references, concrete
file paths, and actionable remediation.

## Pre-execution Checklist (Phase 0)

Before scanning, determine the following by inspecting the workspace and
interpreting the user's request:

1. **Audit Depth**
   - `full` (default if not specified) — execute all 11 phases.
   - `quick` — skip deep static analysis and dynamic testing; run phases
     0–2, 3 (surface-level), 4 (surface-level), 7 (surface-level), 11 only.
   - `formal` — enable compliance mapping and SAST/DAST in addition to full.

2. **Compliance Frameworks** (off by default)
   - Enable only if user explicitly requests GDPR, SOC2, PCI-DSS, or `formal`.
   - Flag: `COMPLIANCE=gdpr,soc2,pcidss` (any subset).

3. **SAST / DAST** (off by default)
   - Enable only if user explicitly requests static/dynamic testing or `formal`.
   - DAST must be **non-destructive** (read-only HTTP requests, no mutations).
   - Flag: `SAST=1`, `DAST=1`.

4. **Scope Boundaries**
   - Respect `.gitignore` and `node_modules/` / `vendor/` exclusions.
   - Do not audit third-party code unless asked.
   - If the project has multiple modules/apps, default to the root; narrow only
     if the user specifies a sub-path.

5. **Output Directory**
   - Default: `./security-audit-outputs/` (create if missing).
   - Override if the user provides a path.

## Execution Phases

### Phase 1 — Scope & Reconnaissance
- Map the technology stack (language, framework, DB, frontend, infra).
- Identify entry points: API routes, controllers, middleware, CLI commands,
  background jobs, external integrations.
- List high-risk files (auth, payment, user input, DB queries, file uploads).
- Document scope assumptions in the report preamble.

### Phase 2 — Dependency & Secret Scan
- **Secret Scan**: Grep for high-entropy strings, known patterns
  (`AKIA...`, `ghp_...`, `sk-...`, `private_key`, `password`, `secret`,
  `api_key`, `token`, `Bearer `) in source files, configs, and env examples.
  Flag hardcoded credentials, even in "test" or "example" files.
- **Dependency Scan**: Check `composer.lock`, `package-lock.json`, `Cargo.lock`,
  `Pipfile.lock`, `go.mod`, `requirements.txt` for known CVEs via
  `grep` + common vulnerability keywords (`CVE-`, `XSS`, `RCE`, `SQLi`,
  `prototype pollution`, `path traversal`). Note: this is a heuristic scan,
  not a live advisory DB lookup; flag anything suspicious for manual review.
- **Container / IaC**: Scan `Dockerfile`, `docker-compose.yml`, `*.tf`,
  `*.yaml` (K8s) for root containers, exposed ports, missing health checks,
  and secrets in env vars.

### Phase 3 — Authentication & Authorization Review
- Inspect JWT/session handling: secret storage, algorithm (`none`, `HS256`
  vs `RS256`), expiry, refresh logic, revocation.
- Review role-based access control (RBAC) implementations — missing checks,
  horizontal/vertical escalation paths.
- Check password policies, hashing algorithms (bcrypt/Argon2 vs MD5/SHA1),
  MFA gaps.
- Audit OAuth / SSO flows: state parameter, redirect URI validation,
  token exchange security.

### Phase 4 — API Security Analysis
- Review all route definitions for missing or misconfigured rate limiting.
- Check CORS policies: overly permissive origins, credentials + wildcard.
- Inspect input validation middleware and exception handling that may leak
  stack traces or internal paths.
- Enumerate sensitive endpoints (admin, export, debug) that lack authentication.
- Assess mass-assignment / auto-binding vulnerabilities.

### Phase 5 — Data Protection & Privacy
- Identify PII fields and their handling (logging, serialization, response
  payloads).
- Check encryption at rest and in transit (TLS version, cipher suites,
  certificate pinning).
- Review data retention, anonymization, and deletion logic.
- Flag insecure direct object references (IDOR) exposing user data.

### Phase 6 — Input Validation & Injection Tests
- Search for raw SQL, string-concatenated queries, `eval`, `exec`,
  `innerHTML`, `dangerouslySetInnerHTML`, and similar injection vectors.
- Check ORM usage for unsafe raw methods (`raw()`, `query()`, ` unprepared()`).
- Inspect file upload flows for extension bypass, path traversal,
  and MIME-type spoofing.
- Validate SSRF protections on outbound HTTP calls.

### Phase 7 — Frontend Security Assessment
- Inspect localStorage / sessionStorage for token storage.
- Check for XSS: `innerHTML`, `document.write`, user-input reflection,
  missing Content-Security-Policy.
- Review CSRF protections on state-changing requests.
- Assess dependency supply-chain risks in `package.json` (typo-squatting heuristics).

### Phase 8 — Infrastructure & Configuration Review
- Review `.env` files for production secrets, debug flags (`APP_DEBUG=true`),
  and weak DB credentials.
- Check logging configuration for sensitive data leakage.
- Inspect CI/CD configs for secret injection, overly permissive permissions,
  and container image security.
- Verify backup / disaster recovery configurations.

### Phase 9 — Compliance Mapping (Conditional)
**Enabled only if `COMPLIANCE` flag is set.**
- Map findings to relevant control frameworks:
  - **GDPR**: Art. 25 (Data Protection by Design), Art. 32 (Security of
    Processing), Art. 33 (Breach Notification).
  - **SOC2**: CC6.1 (Logical Access), CC6.2 (Prior to Access), CC6.3
    (Access Removal), CC7.1 (Detect Security Events), CC7.2 (Vulnerability
    Management).
  - **PCI-DSS**: Req 1 (Firewall), Req 2 (Default Passwords), Req 3
    (Stored Cardholder Data), Req 6 (Secure Systems), Req 8 (User Auth),
    Req 11 (Vulnerability Scans).
- Include a compliance matrix table: Finding × Framework × Control × Status.

### Phase 10 — SAST / DAST (Conditional)
**Enabled only if `SAST=1` or `DAST=1`.**
- **SAST**: Deep static analysis of high-risk files identified in Phase 1.
  Focus on taint analysis (user input → dangerous sink), control-flow issues,
  and business-logic flaws. Document methodology and confidence level.
- **DAST**: Non-destructive dynamic probing of running endpoints (if a dev
  server URL is provided or can be inferred safely). Only safe HTTP methods
  (GET/HEAD/OPTIONS). No mutations, no load tests, no brute-force. Document
  endpoints tested and observed responses.

Produce two files in the output directory:

1. `report.md` — Human-readable full report.
2. `report.json` — Machine-readable structured summary.

### Report structure guarantees (non-negotiable)

Regardless of audit depth (`full`, `quick`, or `formal`) and regardless of whether compliance mapping or SAST/DAST is enabled, the Markdown report **MUST** contain **ALL** of the following sections in this exact order:

1. **Executive Summary** — Risk posture with severity counts table, Top 3 immediate actions, scope assumptions and limitations.
2. **Methodology** — Phases executed (list 1–11, note skipped conditionals), tools used, limitations.
3. **Findings** — One table per severity tier (Critical, High, Medium, Low, Info). Every finding row MUST include:
   - **OWASP Top 10 category** (e.g., A01, A07) — never omit.
   - **CWE ID** (e.g., CWE-798) — never omit.
   - If compliance is enabled, **add** the compliance mapping in the same row or an adjacent column, but do NOT replace OWASP/CWE with it.
4. **Compliance Matrix** (only if compliance enabled) — Appended after Findings.
5. **SAST / DAST Results** (only if enabled) — Appended after Compliance Matrix.
6. **Remediation Roadmap** — Immediate (0–7 days), Short-term (1–4 weeks), Long-term (1–3 months).
7. **Attestation** — Disclaimer that this is automated analysis, not a manual pentest.

If the audit depth is `formal` or compliance is enabled, you may append extra detail (e.g., CVSS scores, STRIDE tables, PCI-DSS requirement mapping) **inside** the relevant sections above, but you must NOT drop the standard sections or reorder them.

#### report.md Template

Use this exact structure. Replace bracketed placeholders.

```markdown
# Security Audit Report — <Project Name>
**Date**: <YYYY-MM-DD>
**Auditor**: Security Audit Expert (AI-assisted)
**Scope**: <description>
**Compliance**: <enabled frameworks or "None">
**SAST/DAST**: <enabled or "None">

## Executive Summary
- Risk posture table: Critical / High / Medium / Low / Info counts
- Top 3 immediate actions (numbered)
- Scope assumptions and limitations (bulleted)

## Methodology
- Phases executed (list 1–11; mark skipped phases as "Skipped — not requested")
- Tools used (grep, read, pattern matching)
- Limitations (no live CVE DB, no destructive testing)

## Findings
### Critical
| ID | Finding | File | Line | OWASP | CWE | Remediation |
|----|---------|------|------|-------|-----|-------------|
| F01 | ... | ... | ... | A07 | CWE-798 | ... |

### High
... (same table columns)

### Medium
... (same table columns)

### Low
... (same table columns)

### Info
... (same table columns)

## Compliance Matrix (if enabled)
| Finding | GDPR | SOC2 | PCI-DSS | Status |
|---------|------|------|---------|--------|

## SAST / DAST Results (if enabled)
### SAST
- Methodology summary
- File-level taint findings

### DAST
- Endpoints tested
- Observed anomalies

## Remediation Roadmap
### Immediate (0–7 days)
### Short-term (1–4 weeks)
### Long-term (1–3 months)

## Attestation
> This audit was performed using automated static analysis and non-destructive
> dynamic probing where enabled. It does not replace a full manual penetration
> test by a certified security engineer.
```

#### report.json Schema

```json
{
  "meta": {
    "project": "string",
    "date": "string",
    "scope": "string",
    "compliance": ["gdpr", "soc2", "pcidss"],
    "sast_enabled": true,
    "dast_enabled": true
  },
  "summary": {
    "critical": 0,
    "high": 0,
    "medium": 0,
    "low": 0,
    "info": 0
  },
  "findings": [
    {
      "id": "F01",
      "severity": "Critical|High|Medium|Low|Info",
      "title": "string",
      "description": "string",
      "affected_files": ["path:line"],
      "owasp": "string",
      "cwe": "string",
      "remediation": "string",
      "compliance_mapping": {
        "gdpr": ["Art. 32"],
        "soc2": ["CC6.1"],
        "pcidss": ["Req 6.5"]
      }
    }
  ],
  "remediation_roadmap": {
    "immediate": ["string"],
    "short_term": ["string"],
    "long_term": ["string"]
  },
  "sast": {
    "methodology": "string",
    "findings": []
  },
  "dast": {
    "endpoints_tested": ["string"],
    "findings": []
  }
}
```

## Severity Definitions

- **Critical**: Exploitable remotely without auth; leads to full compromise,
  data breach, or financial loss. Fix within 24 hours.
- **High**: Exploitable with low privilege or leads to significant data exposure.
  Fix within 7 days.
- **Medium**: Requires specific conditions or attacker effort; limited blast radius.
  Fix within 30 days.
- **Low**: Defense-in-depth gaps, informational misconfigurations. Fix within
  90 days or accept risk.
- **Info**: Best-practice observations, hygiene issues, no direct exploit path.

## Constraints & Safety

- **NEVER modify source code** during the audit.
- **NEVER execute destructive commands** (`rm`, `drop`, `migrate:fresh`, etc.).
- **DAST must be non-destructive**: read-only HTTP methods only; no state
  changes, no brute-force, no fuzzing that generates excessive load.
- If the user provides a running dev server URL for DAST, verify it is a
  local/non-production endpoint before probing.
- Do not audit files outside the declared scope or in `.gitignore`.
- **OWASP and CWE references are mandatory** on every finding — never omit
  them, even when compliance mapping (GDPR/SOC2/PCI-DSS) is enabled.
- Flag any findings that require manual confirmation due to heuristic scanning.
