---
name: paladin-repo-audit
description: Audit a local repository for security-relevant architecture, configuration, dependency, auth, authorization, input-handling, data-flow, secrets, logging, and infrastructure risks. Use for broad security checklists, weekly sweeps, repository security backlog creation, OWASP/CWE baseline review, or public GPT-style codebase audit guidance.
---

# Repository Security Audit

Route repository security work to the right audit mode, then load only the matching reference.

## Load Order

1. Read `PALADIN.md` if present.
2. Choose exactly one mode:
   - Use repository audit mode for broad baseline review, checklist review, public GPT-style codebase guidance, or security backlog creation.
   - Use weekly security sweep mode for recently merged PRs, dependency changes, open findings, known-exploited vulnerabilities, recurring patterns, metrics, or weekly reporting.
3. Read the matching reference:
   - Repository audit: `references/audit-mode.md`
   - Weekly sweep: `references/weekly-sweep-mode.md`
4. Read the configured source list only when current advisory context matters. Use `PALADIN.md` `paladin_source_list`, or default to repo-root `references/security-sources.md`.

## Shared Workflow

1. Inventory the repo quickly:

```bash
git status --porcelain
rg --files
```

2. Identify frameworks, entry points, auth/authz layers, data access, external calls, package manifests, deployment files, and tests.
3. Use OWASP Top 10:2025, CWE Top 25:2025, OWASP ASVS 5.0.0, and CISA KEV as the review baseline.
4. Prefer depth on risky boundaries over shallow coverage everywhere.
5. Do not overclaim coverage or report generic checklist items without observed evidence.

## Current Issue Sources

Use the configured Markdown source list only for recent vulnerabilities, dependency advisories, public zero-day claims, known exploited issues, CVEs, GHSAs, OSV IDs, scanner findings, internet-facing infrastructure, or weekly sweeps.

Download or query advisory metadata only. Do not download proof-of-concept exploit repositories, exploit payloads, weaponized code, or exploit playbooks.

Evaluate each potentially relevant advisory one by one against local repo inventory. Use these results:

- `action required`: the repo appears affected, exposed, or missing the required mitigation
- `already protected`: the exploit or advisory exists, but this repo uses a fixed or unaffected version, or an existing control blocks the issue
- `not relevant`: no matching product, package, version, or reachable component exists
- `needs verification`: local evidence is incomplete

Report only `action required` and `needs verification` by default. Include `already protected` only when useful, such as a high-profile exploit where the repo already uses the safe package version. Omit `not relevant` items unless the user asks for a full audit trail.

Record source, query, date checked, and result when current source lookup affects the audit or weekly sweep. If lookup is needed but unavailable, mark it as `not available` and continue with local repo evidence.

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
