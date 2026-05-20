# Repository Audit Mode

Use this reference for broad repository security audits and security backlog creation.

## Contents

- Audit workflow
- Review areas
- Backlog gate
- Stable values
- Output contract
- Final self-check

## Audit Workflow

1. Sample security-relevant paths first.
2. Inspect framework entry points, auth/authz controls, data access, external calls, package manifests, deployment files, and tests.
3. Check current issue sources only when dependency, runtime, infrastructure, scanner, CVE, GHSA, OSV, public zero-day, or known-exploited context matters.
4. Produce a prioritized backlog with evidence, impact, mitigation, and test requirements.
5. If a finding needs code changes, route to `paladin-mitigate` for implementation planning.

## Review Areas

- Authentication: sessions, token validation, MFA, password reset, cookie settings
- Authorization: roles, object ownership, tenant isolation, admin boundaries, server-side checks
- Input handling: API params, webhooks, file uploads, parsers, redirects, templates, HTML
- Data access: ORM queries, raw SQL, search filters, object IDs, export paths
- External calls: SSRF controls, URL allowlists, request signing, timeout and retry behavior
- Serialization: JSON, XML, YAML, pickle, archive extraction
- Crypto and secrets: hashing, encryption, key storage, env vars, logs, client bundles
- Dependencies: lockfiles, new packages, known exploited CVEs, build scripts
- Infrastructure: Docker, CI/CD, IaC, CORS, headers, public buckets, debug config
- Logging and errors: sensitive data, audit events, stack traces, alerting gaps

## Backlog Gate

Only include a backlog item when it has:

- repo evidence
- a plausible affected actor, asset, or boundary
- a concrete security impact
- a mitigation or verification step
- an owner-ready next action

Cap output at 10 backlog items. Sort by priority, then severity. Do not fill the list with generic checklist items.

## Stable Values

- Priority: `P0`, `P1`, `P2`, `P3`
- Severity: `Medium`, `High`, `Critical`
- Blocking now: `yes`, `no`
- Overall risk: `Low`, `Medium`, `High`, `Critical`
- Coverage: `sampled`, `partial`, `not reviewed`
- Reasoning: reason privately; report concise evidence-backed conclusions only

## Output Contract

Open with a CEO-readable summary. Keep it to 5 bullets or fewer. Define unavoidable terms in plain English before using acronyms.

```text
Security check result: Action required / No action needed / Needs verification

- What Paladin checked:
- What matters:
- What needs action:
- What is already safe:
- Owner:
```

Then use:

```text
Repository security audit

Scope:
- Repo/components reviewed:
- High-risk areas sampled:
- Areas not reviewed:

Overall risk:
- Low / Medium / High / Critical
- Reason:

Current issue sources checked:
- Source:
- Query:
- Date checked:
- Result:

Advisory feed review:
- Feeds checked:
- Records evaluated:
- Action required:
- Needs verification:
- Already protected, if useful:
```

For each backlog item:

```text
[P0/P1/P2/P3] [Medium/High/Critical] [Title]
- Category: OWASP / CWE / ASVS
- Evidence:
- Impact:
- Recommended mitigation:
- Required test or verification:
- Blocking now: yes/no
```

Close with:

```text
Next review focus:
- [one or two highest-value next checks]

Coverage note:
- This audit is a prioritized review of observed evidence, not a complete penetration test or zero-day assessment.
```

## Final Self-Check

Before finalizing, verify:

- The output uses only stable values from this reference.
- The opening summary is understandable to a CEO with no security background.
- Every backlog item has repo evidence, impact, mitigation, and required test or verification.
- Current issue source lookup is recorded when recent vulnerability or dependency context affects the audit.
- Advisory records are evaluated one by one when source feeds are used, but non-issues are omitted unless useful.
- Advisory feed review includes counts for feeds checked, records evaluated, action required, and needs verification.
- No generic checklist item is reported as a finding without observed evidence.
- Coverage limits are stated clearly.
