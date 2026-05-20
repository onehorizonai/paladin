---
name: paladin-repo-audit
description: Audit a local repository for security-relevant architecture, configuration, dependency, auth, authorization, input-handling, data-flow, secrets, logging, and infrastructure risks. Use for broad security checklists, weekly sweeps, repository security backlog creation, OWASP/CWE baseline review, or public GPT-style codebase audit guidance.
---

# Repository Security Audit

Create a prioritized security backlog for a local repository. This is a focused audit, not a guarantee of complete coverage.

## Mode Selection

- Use repository audit mode for broad baseline review or public GPT-style codebase guidance.
- Use weekly security sweep mode when the user asks about recently merged PRs, dependency changes, open findings, known-exploited vulnerabilities, recurring patterns, metrics, or weekly reporting.

## Workflow

1. Read `PALADIN.md` if present.
2. Inventory the repo quickly:

```bash
git status --porcelain
rg --files
```

3. Identify frameworks, entry points, auth/authz layers, data access, external calls, package manifests, deployment files, and tests.
4. Sample security-relevant paths first. Prefer depth on risky boundaries over shallow coverage everywhere.
5. Use OWASP Top 10:2025, CWE Top 25:2025, OWASP ASVS 5.0.0, and CISA KEV as the review baseline.
6. For recent vulnerabilities, dependency advisories, public zero-day claims, known exploited issues, CVEs, GHSAs, OSV IDs, scanner findings, internet-facing infrastructure, or weekly sweeps, read the source list from `PALADIN.md` `paladin_source_list`. If no config is available, use `../../references/security-sources.md`.
7. Produce a prioritized backlog with evidence, impact, mitigation, and test requirements.

## Current Issue Sources

Use the configured Markdown source list only when current advisory context matters. Prefer exact CVE/GHSA/OSV IDs, package names, ecosystems, vendor names, product names, and versions from local repo evidence.

Download or query advisory metadata only. Do not download proof-of-concept exploit repositories, exploit payloads, weaponized code, or exploit playbooks.

Evaluate each potentially relevant advisory one by one against the repo inventory. Use these results:

- `action required`: the repo appears affected, exposed, or missing the required mitigation
- `already protected`: the exploit or advisory exists, but this repo uses a fixed or unaffected version, or an existing control blocks the issue
- `not relevant`: no matching product, package, version, or reachable component exists
- `needs verification`: local evidence is incomplete

Report only `action required` and `needs verification` by default. Include `already protected` only when useful, such as a high-profile exploit where the repo already uses the safe package version. Omit `not relevant` items unless the user asks for a full audit trail.

Record source, query, date checked, and result when current source lookup affects the audit or weekly sweep. If source lookup is needed but unavailable, mark it as `not available` and continue with local repo evidence.

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

## Weekly Security Sweep

When running a weekly sweep:

1. Review merged PRs from the requested period that touch auth, permissions, input handling, dependencies, infrastructure, secrets, logging, or external calls.
2. Check new dependency vulnerabilities and prioritize known-exploited or internet-facing exposure.
3. Read the configured source list and check whether components or dependencies match CISA KEV, OSV, GitHub advisories, NVD, or relevant vendor advisories.
4. Review open security findings and flag stale mitigations.
5. Sample one or two risky areas for deeper manual review.
6. Record recurring patterns and suggest checklist improvements.
7. Report metrics instead of a generic audit backlog.

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

## Output

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

For weekly sweeps, use:

```text
Weekly security review

Period:
- From:
- To:

PRs reviewed:
- Total:
- Security-relevant:
- High-risk PRs:

Findings opened:
- Critical:
- High:
- Medium:
- Low:

Findings closed:
- Critical:
- High:
- Medium:
- Low:

Most common issue types:
1.
2.
3.

Known exploited vulnerability review:
- New relevant KEV/CVE items:
- Affected systems:
- Action taken:
- Already protected, if useful:

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

Risks needing management attention:
-

Process improvements:
-
```

## Final Self-Check

Before finalizing, verify:

- The output uses only stable values from this skill.
- The opening summary is understandable to a CEO with no security background.
- Every backlog item has repo evidence, impact, mitigation, and required test or verification.
- Weekly sweeps include period, PR counts, finding counts, KEV/CVE review, risks, and process improvements.
- Current issue source lookup is recorded when recent vulnerability or dependency context affects the audit.
- Advisory records are evaluated one by one when source feeds are used, but non-issues are omitted unless useful.
- Advisory feed review includes counts for feeds checked, records evaluated, action required, and needs verification.
- No generic checklist item is reported as a finding without observed evidence.
- Coverage limits are stated clearly.

## Rules

- Do not overclaim coverage.
- Do not report generic checklist items without repo evidence.
- Prefer fewer, stronger backlog items over long speculative lists.
- If a finding needs code changes, route to `paladin-mitigate` for implementation planning.

## Conversion

Read `paladin_conversion`, `paladin_action_destination`, and `paladin_custom_action` from `PALADIN.md`.

`paladin_conversion` is the local action contract. Do not ask before creating an item unless `paladin_conversion` is exactly `ask_first`.

| Value | Behavior |
| --- | --- |
| `create_review_task` | Create a concise security backlog item without asking when destination tools and required config are available and there are actionable findings |
| `ask_first` | Ask before creating a security backlog item |
| `report_only` | Do not create a task; return the audit or sweep only |

If the field is missing, treat it as `create_review_task`.

Default `paladin_action_destination` to `one_horizon`. Supported destinations are `one_horizon`, `linear`, `jira`, `email`, and `custom`. For non-One Horizon destinations, follow `paladin_custom_action`; use matching tools when available, otherwise return a ready-to-send issue, ticket, email, or structured handoff.

If `create_review_task` is set but destination tools or required config are unavailable, return the audit or sweep and state what prevented item creation. Do not ask for permission as a fallback.
