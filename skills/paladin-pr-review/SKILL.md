---
name: paladin-pr-review
description: Review a pull request, diff, staged changes, or uncommitted local changes for high-confidence security vulnerabilities. Use for PR security review, code security review, OWASP/CWE review of changed code, or security-sensitive diffs touching auth, authorization, input handling, data access, external calls, secrets, dependencies, infrastructure, logging, or file handling.
---

# PR Security Review

Review changed code for high-risk vulnerability classes, known exploited weaknesses, and regressions against the application security baseline. This is a focused PR review, not unknown zero-day discovery.

## Load Order

1. Read `PALADIN.md` if present.
2. Read `references/output-contract.md` before writing the final review, posting comments, or making a task.
3. Read the configured source list only when current advisory context matters. Use `PALADIN.md` `paladin_source_list`, or default to repo-root `references/security-sources.md`.

## Scope

- Review only added or modified code unless unchanged code is required to prove exploitability.
- Do not report pre-existing unrelated issues.
- Use OWASP Top 10:2025, CWE Top 25:2025, OWASP ASVS 5.0.0, and CISA KEV as the baseline.
- Reason privately. Report concise evidence-backed conclusions only.

## Workflow

1. Inspect the PR, staged changes, or local diff:

```bash
git diff
git diff --cached
git diff --stat
```

2. Classify whether the change touches authentication, authorization, input handling, data access, external calls, serialization, crypto, dependencies, infrastructure, logging, errors, secrets, file uploads, webhooks, or admin flows.
3. Build the threat model before judging findings:
   - reachable actor
   - protected data or privilege
   - crossed trust boundary
   - attacker-controlled input
   - worst plausible impact
   - assumptions about auth, authorization, validation, or environment
4. Check current issue sources only for dependency, package, runtime, infrastructure, scanner, CVE, GHSA, OSV, public zero-day, or known-exploited context.
5. Check available automation before manual review: SAST, dependency/SCA, secret scan, IaC/container scan, tests, lint, and type checks. Mark unavailable checks as `not available`.
6. For every candidate issue, trace attacker-controlled input to the real sink and verify existing controls first.
7. Report only medium, high, or critical findings that pass the gate in `references/output-contract.md`.

## Current Issue Sources

Use only relevant official, vendor, ecosystem, and advisory database sources from the configured source list. Download or query advisory metadata only. Do not download proof-of-concept exploit repositories, exploit payloads, weaponized code, or exploit playbooks.

Evaluate potentially relevant advisories one by one against changed dependency, package, runtime, container, infrastructure, or framework evidence. Report only records that need action or verification by default. Include `already protected` only when useful, such as a high-profile exploit where the PR already uses the safe package version.

If source lookup affects the review, include the source, query, date checked, and result in the output. If no current lookup is needed, mark the advisory source check as `not applicable`. If lookup is needed but unavailable, mark it as `not available`.

## Priorities

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
