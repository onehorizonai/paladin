---
name: paladin-code-review
description: Review changed code for high-confidence security vulnerabilities. Use for uncommitted local changes, staged changes, branch diffs, pasted diffs, or code security review before a PR when the task is focused on the diff rather than a broad repository audit.
---

# Code Security Review

Review changed code for high-risk vulnerability classes, known exploited weaknesses, and security regressions. This is a defensive, read-only review of a diff, not unknown zero-day discovery.

## Load Order

1. Read `PALADIN.md` if present.
2. Read `references/output-contract.md` before writing the final review, posting comments, or making a task.
3. Read the configured source list only when current advisory context matters. Use `PALADIN.md` `paladin_source_list`, or default to repo-root `references/security-sources.md`.

## Scope Resolution

Use the first clear scope:

1. Explicit pasted diff, file list, branch range, or user-selected paths.
2. Staged changes:

```bash
git diff --cached --name-only
git diff --cached
```

3. Unstaged or uncommitted changes, including new files:

```bash
git diff --name-only
git diff
git ls-files --others --exclude-standard
```

4. Branch diff against the default branch or merge base when no working-tree diff exists.
5. PR diff when supplied by a PR wrapper such as `paladin-pr-review`.

Review only changed code plus unchanged surrounding code required to prove reachability, controls, or exploitability. Do not report unrelated pre-existing issues.

## Workflow

1. Inventory changed files, changed dependencies, package scripts, deployment files, and tests.
2. Classify risky touchpoints: authentication, authorization, tenant isolation, input handling, data access, external calls, serialization, crypto, dependencies, infrastructure, logging, errors, secrets, file uploads, webhooks, admin flows, and package scripts.
3. Ask what security property changed: new trust boundary, removed validation, broader permissions, weaker cryptography, new dependency, changed package script, new external call, or larger blast radius.
4. Build misuse cases before judging findings:
   - reachable actor
   - protected data or privilege
   - crossed trust boundary
   - attacker-controlled input
   - source-to-sink path
   - expected security control
   - test or automation that proves the control
5. Trace untrusted input to sensitive sinks across files. Prefer "this request field reaches this raw SQL call" over "this API is often dangerous."
6. Check existing auth, authorization, validation, escaping, parameterization, allowlists, framework protections, and type constraints before reporting.
7. Check available automation: tests, type checks, SAST, dependency/SCA, secret scan, IaC/container scan, lint, and package audit. Mark unavailable checks as `not available`.
8. Check current issue sources only for dependency, package, runtime, infrastructure, scanner, CVE, GHSA, OSV, public zero-day, or known-exploited context.
9. Report only High-confidence Medium, High, or Critical findings that pass the gate in `references/output-contract.md`.

## Review Heuristics

- Prefer risk-first depth over checklist breadth. Small diffs can be high risk when they touch auth, validation, crypto, dependency resolution, external calls, or value transfer.
- Treat environment variables, server-side config, CLI flags, hardcoded constants, and deployment settings as trusted unless repo evidence shows attacker control.
- Account for framework protections before reporting. Auto-escaped templates, parameterized ORMs, safe serializers, and client framework escaping usually block common false positives; unsafe escape hatches, raw APIs, disabled escaping, and string interpolation still need review.
- Validate authorization on the server boundary for every sensitive object or action. Client-side role checks are UX only.
- Review secrets and dependency changes as explicit checks, but separate dependency hygiene from exploitability findings unless supply-chain risk is in scope.
- Assign severity by impact and exploitability, not by scary API names.
- Use standards as vocabulary, not as filler: OWASP Top 10:2025, CWE Top 25:2025, OWASP ASVS 5.0.0, NIST SSDF, and CISA KEV.
- Follow secure code review practice from OWASP: manual review still matters because scanners miss business logic, authorization, and context-specific trust-boundary failures.
- Be honest about coverage and confidence. State sampled areas and unavailable checks instead of implying complete coverage.
- Keep evidence defensive. Do not provide exploit payloads, weaponized steps, stealth, persistence, credential abuse, or live-target scanning guidance.

## Current Issue Sources

Use only relevant official, vendor, ecosystem, and advisory database sources from the configured source list. Download or query advisory metadata only. Do not download proof-of-concept exploit repositories, exploit payloads, weaponized code, or exploit playbooks.

Evaluate potentially relevant advisories one by one against changed dependency, package, runtime, container, infrastructure, or framework evidence. Report only records that need action or verification by default. Include `already protected` only when useful, such as a high-profile exploit where the diff already uses the safe package version.

If source lookup affects the review, include the source, query, date checked, and result in the output. If no current lookup is needed, mark the advisory source check as `not applicable`. If lookup is needed but unavailable, mark it as `not available`.

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
