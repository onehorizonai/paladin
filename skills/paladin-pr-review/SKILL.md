---
name: paladin-pr-review
description: Review a pull request, diff, staged changes, or uncommitted local changes for high-confidence security vulnerabilities. Use for PR security review, code security review, OWASP/CWE review of changed code, or security-sensitive diffs touching auth, authorization, input handling, data access, external calls, secrets, dependencies, infrastructure, logging, or file handling.
---

# PR Security Review

Review code changes for high-risk vulnerability classes, known exploited weaknesses, and regressions against the application security baseline.

## Scope

Review only added or modified code unless unchanged code is required to prove exploitability. Do not report pre-existing unrelated issues.

Use these baselines:

- OWASP Top 10:2025 for web-app risk categories
- CWE Top 25:2025 for concrete weakness classes
- OWASP ASVS 5.0.0 for control expectations
- CISA KEV when dependency or known-exploited vulnerability risk is relevant

## Current Issue Sources

When the review depends on recent vulnerabilities, dependency advisories, public zero-day claims, known exploited issues, CVEs, GHSAs, OSV IDs, scanner findings, or newly changed package versions, read the source list from `PALADIN.md` `paladin_source_list`. If no config is available, use `../../references/security-sources.md`.

Use only relevant sources from that list. Prefer official, vendor, ecosystem, and advisory database sources. Do not rely on model memory for current issues.

Download or query advisory metadata only. Do not download proof-of-concept exploit repositories, exploit payloads, weaponized code, or exploit playbooks.

Evaluate each potentially relevant advisory one by one against the changed dependency, package, runtime, container, infrastructure, or framework evidence. Use these results:

- `action required`: the PR appears affected, exposed, or missing the required mitigation
- `already protected`: the exploit or advisory exists, but this PR uses a fixed or unaffected version, or an existing control blocks the issue
- `not relevant`: no matching product, package, version, or reachable component exists
- `needs verification`: local evidence is incomplete

Report only `action required` and `needs verification` by default. Include `already protected` only when useful, such as a high-profile exploit where the PR already uses the safe package version. Omit `not relevant` items unless the user asks for a full audit trail.

If source lookup affects the review, include the source, query, date checked, and result in the output. If no current lookup is needed, mark the advisory source check as `not applicable`. If lookup is needed but unavailable, mark it as `not available`.

## Workflow

1. Read `PALADIN.md` if present.
2. Inspect the diff and surrounding code paths:

```bash
git diff
git diff --cached
git diff --stat
```

3. Classify whether the change touches authentication, authorization, input handling, data access, external calls, serialization, crypto, dependencies, infrastructure, logging, errors, secrets, file uploads, webhooks, or admin flows.
4. Answer the threat model before judging findings:
   - What user, system, or external actor can reach this code?
   - What data or privilege does the code protect?
   - What trust boundary is crossed?
   - What could an attacker control?
   - What is the worst plausible impact if this code is wrong?
   - What assumptions does the code make about authentication, authorization, validation, or environment?
5. Check current issue sources when the diff includes dependency, package, runtime, infrastructure, scanner, CVE, GHSA, OSV, or known-exploited context.
6. Check available automation before manual review:
   - SAST or code scanning
   - dependency/SCA scan
   - secret scan
   - IaC or container scan
   - unit, integration, or security tests
   - lint and type checks
   Mark unavailable checks as `not available`; do not invent passing status.
7. For every candidate issue, trace attacker-controlled input to the real sink.
8. Verify existing controls before reporting:
   - auth or permission checks
   - schema validation or type constraints
   - framework escaping
   - ORM parameterization
   - allowlists or bounded constants
   - environment or deployment guards
9. Report only medium, high, or critical findings with a plausible attack path and concrete code evidence.

## Finding Gate

Only report a candidate when every gate passes:

| Gate | Requirement |
| --- | --- |
| Changed scope | Introduced or exposed by the PR, or unchanged code is needed to prove exploitability |
| Reachability | A realistic actor can reach the vulnerable path |
| Control failure | Existing auth, validation, escaping, parameterization, allowlists, or type constraints do not block it |
| Impact | Plausible confidentiality, integrity, availability, privilege, tenant, secret, or supply-chain impact |
| Actionability | There is a concrete mitigation and regression test |
| Severity | Medium, High, or Critical |

If any gate fails, omit the candidate. Do not include a "maybe" finding.

## Stable Values

- Severity: `Medium`, `High`, `Critical`
- Blocking: `Yes`, `No`
- Decision: `Approved`, `Approved with non-blocking follow-up`, `Changes requested`, `Escalated for deeper security review`
- Check status: `passed`, `failed`, `not applicable`, `not available`
- Maximum findings: 5, sorted by severity and exploitability
- Reasoning: reason privately; report concise evidence-backed conclusions only

## What To Prioritize

- Broken access control, IDOR, tenant bypass, role confusion
- SQL, NoSQL, command, template, code, path, or LDAP injection
- SSRF, XSS, CSRF, unsafe redirect, request forgery
- Unsafe deserialization, XML/YAML parser abuse, prototype pollution
- Secret leakage, insecure logging, debug errors, public storage exposure
- Dangerous dependency, package script, lockfile, or supply-chain change
- Fail-open security logic or missing mitigation tests

## Do Not Report

- Speculative concerns without a reachable path
- Pure style issues
- Low-severity hardening ideas
- Findings blocked by existing validation, escaping, parameterization, authz, or type constraints
- Exploit steps that would enable abuse outside defensive review

## Output

Open with a CEO-readable summary before the detailed contract. Keep it to 5 bullets or fewer. Define unavoidable terms in plain English before using acronyms. If there are no findings, say `No action needed` and write `Findings: None`.

```text
Security check result: Action required / No action needed / Needs verification

- What changed:
- What Paladin checked:
- What matters:
- What to do next:
- Owner:
```

Then use this output contract. Keep each field concise.

```text
Security review summary

Scope reviewed:
- Files/components:
- Entry points:
- Data or privilege affected:

Risk classification:
- Low / Medium / High / Critical
- Reason:

Checks performed:
- OWASP Top 10 review: passed / failed / not applicable / not available
- CWE Top 25 review: passed / failed / not applicable / not available
- ASVS control review: passed / failed / not applicable / not available
- Current advisory source check: passed / failed / not applicable / not available
- Dependency/SCA review: passed / failed / not applicable / not available
- Secret scan: passed / failed / not applicable / not available
- Auth/authz review: passed / failed / not applicable / not available
- Input validation review: passed / failed / not applicable / not available
- Logging/error handling review: passed / failed / not applicable / not available

Threat model:
- Reachable by:
- Protected data or privilege:
- Trust boundary:
- Attacker-controlled input:
- Worst plausible impact:
- Security assumptions:

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

Findings:
```

For each finding:

```text
Finding: [Title]

Category:
- OWASP:
- CWE:
- ASVS:

Evidence:
- [file:line plus concrete code behavior]

Exploit scenario:
- [bounded defensive scenario]

Impact:
- [who or what is affected]

Recommended mitigation:
- [smallest concrete fix]

Required test:
- [regression test that proves the control]

Severity:
- Medium / High / Critical

Blocking:
- Yes / No
```

Close with:

```text
Decision:
- Approved
- Approved with non-blocking follow-up
- Changes requested
- Escalated for deeper security review
```

## Automation Outputs

When GitHub, PR review, or Slack tools are available and the user requested automation:

- Review previous unresolved security-review threads from earlier runs.
- Validate whether each prior finding still applies.
- Re-report any finding that still holds with fresh evidence.
- Resolve or supersede previous automation review threads only when the current tool supports it and the user has authorized that workflow.
- Post inline PR comments on exact diff lines for current findings.
- Keep each inline comment concise: severity, issue, impact, and required fix.
- If no high-confidence vulnerability remains, leave no new finding comments.
- Post a short Slack summary only when Slack tooling is available and configured.
- Do not push changes or open fix PRs from this workflow.

## Final Self-Check

Before finalizing, verify:

- The output uses only stable values from this skill.
- The opening summary is understandable to a CEO with no security background.
- Every finding has evidence, attack scenario, impact, mitigation, required test, severity, and blocking status.
- No finding is blocked by existing auth, validation, escaping, parameterization, allowlists, or type constraints.
- Current advisory source lookup is recorded when recent vulnerability or dependency context affects the review.
- Advisory records are evaluated one by one when source feeds are used, but non-issues are omitted unless useful.
- Advisory feed review includes counts for feeds checked, records evaluated, action required, and needs verification.
- The review does not include exploit playbooks or weaponized payloads.
- The decision follows from the findings.

## Conversion

Read `paladin_conversion`, `paladin_action_destination`, and `paladin_custom_action` from `PALADIN.md`.

`paladin_conversion` is the local action contract. Do not ask before creating an item unless `paladin_conversion` is exactly `ask_first`.

| Value | Behavior |
| --- | --- |
| `create_review_task` | Create a concise review item without asking when destination tools and required config are available and there are actionable findings |
| `ask_first` | Ask before creating a review item |
| `report_only` | Do not create a task; return the review only |

If the field is missing, treat it as `create_review_task`. Do not create tasks for clean reviews unless the user asks.

Default `paladin_action_destination` to `one_horizon`. Supported destinations are `one_horizon`, `linear`, `jira`, `email`, and `custom`. For non-One Horizon destinations, follow `paladin_custom_action`; use matching tools when available, otherwise return a ready-to-send issue, ticket, email, or structured handoff.

If `create_review_task` is set but destination tools or required config are unavailable, return the review and state what prevented item creation. Do not ask for permission as a fallback.
