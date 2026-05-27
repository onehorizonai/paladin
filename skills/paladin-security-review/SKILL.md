---
name: paladin-security-review
description: Run a broad defensive security review of a local repository, including changed-code review, repo sweep, misuse-case analysis, dependency and advisory checks, public zero-day claim evaluation, and known-exploited vulnerability review. Use for security sweeps, broad audits, current vulnerability checks, or requests to check whether a repo is exposed.
---

# Security Review

Run a broad Paladin security workflow across repository evidence, current advisory context, and changed code. This is a defensive review and does not claim unknown zero-day discovery or complete coverage.

## Load Order

1. Read `PALADIN.md` if present.
2. Read `references/output-contract.md` before writing the final report or creating work.
3. Read `../paladin-code-review/SKILL.md` and `../paladin-code-review/references/output-contract.md` when local changes, staged changes, branch diffs, or PR diffs exist.
4. Read `../paladin-repo-audit/references/audit-mode.md` only when the user wants a backlog-style audit or when broad repo evidence produces actionable findings.
5. Read the configured source list when current advisory context matters. Use `PALADIN.md` `paladin_source_list`, or default to repo-root `references/security-sources.md`.

## Workflow

1. Confirm the target is local code the user controls or is authorized to assess.
2. Inventory the repo quickly:

```bash
git status --porcelain
rg --files
```

3. Identify manifests, lockfiles, runtimes, frameworks, auth/authz layers, entry points, data stores, external calls, file handling, CI/CD, containers, deployment config, and tests.
4. If changed, staged, branch, pasted, or PR diffs exist, run the `paladin-code-review` workflow first and include its result as the changed-code pass.
5. Build misuse cases around the highest-risk boundaries:
   - unauthenticated or low-privilege actor reaching protected routes
   - cross-tenant or object ownership bypass
   - attacker-controlled input reaching query, command, template, URL fetch, parser, file, or serialization sinks
   - secrets, credentials, tokens, PII, or internal errors leaking through logs, storage, clients, or responses
   - dependency, build, package-script, container, or CI/CD changes increasing supply-chain risk
6. Trace untrusted inputs to sensitive sinks and privilege boundaries before reporting code findings. Do not treat trusted config or framework-protected APIs as vulnerabilities without repo evidence.
7. Run secrets and dependency checks as explicit phases across source, config, Docker, CI/CD, IaC, package manifests, lockfiles, and logs.
8. Check current issue sources only for dependency, runtime, infrastructure, scanner, CVE, GHSA, OSV, public zero-day, or known-exploited context.
9. Evaluate relevant advisory records one by one against local inventory and exposure. Use `action required`, `needs verification`, `already protected`, or `not relevant`.
10. Report only High-confidence evidence-backed Medium, High, or Critical findings, plus `needs verification` items where missing evidence blocks a clear decision.

## Advisory And Zero-Day Rules

- Treat "zero-day" as a public claim or advisory lead that must be verified against official, vendor, ecosystem, or advisory database sources.
- Do not rely on model memory for recent issues. Use the configured source list when current security context matters.
- Download or query advisory metadata only. Never download proof-of-concept exploit repositories, exploit payloads, weaponized code, exploit playbooks, credential dumps, or live-target scanners.
- Check CISA KEV for known-exploited CVEs when CVEs, dependencies, internet-facing products, or infrastructure components are in scope.
- Prefer OSV, GitHub Advisories, NVD, language ecosystem advisories, and vendor security pages for package and platform version matching.
- Include a compact audit trail whenever advisory lookup affects the result: feeds checked, records evaluated, action required, needs verification, and useful already-protected items.

## Review Baseline

Use these sources as vocabulary and prioritization:

- OWASP Code Review Guide and Secure Coding Practices for manual review of context-specific controls.
- OWASP Top 10:2025 for broad web application risk categories.
- CWE Top 25:2025 for concrete weakness classes.
- OWASP ASVS 5.0.0 for verification control expectations.
- NIST SSDF for secure-development review and recurrence prevention.
- CISA KEV for active exploitation priority.
- Public security-review skill patterns that are worth retaining: read-only review, explicit scope, risk-first triage, evidence-backed findings, prior-control verification, honest coverage limits, and concise remediation/test output.
- Differential-review patterns that are worth retaining: ask what security property changed, check removed controls, estimate blast radius, and use git context when it clarifies newly introduced risk.

## Conversion

Read `paladin_conversion`, `paladin_action_destination`, and `paladin_custom_action` from `PALADIN.md`.

`paladin_conversion` is the local action contract. Do not ask before creating an item unless `paladin_conversion` is exactly `ask_first`.

| Value | Behavior |
| --- | --- |
| `create_review_task` | Create concise security review or backlog items without asking when destination tools and required config are available and there are actionable findings |
| `ask_first` | Ask before creating review or backlog items |
| `report_only` | Do not create tasks; return the report only |

If the field is missing, treat it as `create_review_task`. Do not create tasks for clean reviews unless the user asks.

Default `paladin_action_destination` to `one_horizon`. Supported destinations are `one_horizon`, `linear`, `jira`, `email`, and `custom`. For non-One Horizon destinations, follow `paladin_custom_action`; use matching tools when available, otherwise return a ready-to-send issue, ticket, email, or structured handoff.

If `create_review_task` is set but destination tools or required config are unavailable, return the report and state what prevented item creation. Do not ask for permission as a fallback.
