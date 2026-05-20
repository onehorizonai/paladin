---
name: paladin-mitigate
description: Turn a known vulnerability, CVE, CWE, dependency alert, scanner result, audit finding, or PR security finding into concrete mitigation steps and regression tests. Use when the user asks how to fix, remediate, patch, reduce risk, or validate a security issue.
---

# Mitigation Planner

Convert a known security weakness into the smallest defensible fix plan and the tests that prove it works.

## Workflow

1. Read `PALADIN.md` if present.
2. Normalize the finding:
   - affected component
   - vulnerability class
   - attacker-controlled input
   - sink or failed control
   - current mitigation, if any
   - production reachability
   - confidence: confirmed / likely / uncertain
3. Map it to OWASP Top 10:2025, CWE Top 25:2025 when applicable, and ASVS 5.0.0 controls.
4. If the finding includes a CVE, GHSA, OSV ID, dependency alert, scanner result, public zero-day claim, known-exploited claim, or "latest/recent" issue context, read the source list from `PALADIN.md` `paladin_source_list`. If no config is available, use `../../references/security-sources.md`.
5. Inspect the local code path if available. Prefer existing components, validators, middleware, authorization helpers, and test patterns.
6. Propose the smallest mitigation that closes the attack path.
7. Require regression tests for both allowed and denied behavior where authorization or validation is involved.
8. Verify the plan against the original finding before finalizing.

## Current Issue Sources

Use the configured Markdown source list to verify affected versions, fixed versions, exploit status, and vendor mitigation when the issue is advisory-driven or current. Prefer official, vendor, ecosystem, and advisory database sources.

Download or query advisory metadata only. Do not download proof-of-concept exploit repositories, exploit payloads, weaponized code, or exploit playbooks.

Evaluate each potentially relevant advisory one by one against the repo evidence. Use these results:

- `action required`: the repo appears affected, exposed, or missing the required mitigation
- `already protected`: the exploit or advisory exists, but this repo uses a fixed or unaffected version, or an existing control blocks the issue
- `not relevant`: no matching product, package, version, or reachable component exists
- `needs verification`: local evidence is incomplete

Report only `action required` and `needs verification` by default. Include `already protected` only when useful, such as a high-profile exploit where the repo already uses the safe package version. Omit `not relevant` items unless the user asks for a full audit trail.

Record source, query, date checked, and result when the lookup changes the plan. If lookup is needed but unavailable, list it as missing evidence and keep the plan conservative.

## Stable Values

- Severity: `Medium`, `High`, `Critical`
- Confidence: `confirmed`, `likely`, `uncertain`
- Fix type: `code`, `config`, `dependency`, `infrastructure`, `test`, `process`
- Reasoning: reason privately; report concise evidence-backed conclusions only

## Mitigation Rules

- Fix the security boundary at the server-side owner of the control, not only in UI or caller code.
- Prefer deny-by-default authorization, allowlists, parameterized APIs, safe parsers, bounded constants, redaction, and least privilege.
- Do not invent new security frameworks when the repo already has a control pattern.
- Do not provide exploit playbooks, stealth guidance, credential abuse, persistence, or weaponized payloads.
- If exploitability cannot be proven from the available evidence, state the missing evidence and provide a safe verification plan.

## Output

Open with a CEO-readable summary. Keep it to 5 bullets or fewer. Define unavoidable terms in plain English before using acronyms.

```text
Security check result: Action required / No action needed / Needs verification

- What happened:
- What it means:
- What to do next:
- What is already safe:
- Owner:
```

Then use:

```text
Mitigation plan: [Finding title]

Classification:
- OWASP:
- CWE:
- ASVS:
- Severity:
- Confidence:
- Fix type:

Confirmed evidence:
- [what is known]

Missing evidence:
- [what still needs verification, if anything]

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

Attack path:
- [bounded defensive explanation]

Fix:
- [smallest code/control change]

Regression tests:
- [allowed case]
- [denied/blocked case]
- [edge or error case if relevant]

Rollout:
- [migration, config, dependency upgrade, or monitoring note if needed]

Residual risk:
- [what remains after the fix]
```

## Final Self-Check

Before finalizing, verify:

- The output uses only stable values from this skill.
- The opening summary is understandable to a CEO with no security background.
- The fix closes the stated attack path, not only the visible symptom.
- Current advisory source lookup is recorded when the finding depends on CVE, GHSA, OSV, dependency, known-exploited, or recent issue context.
- Advisory records are evaluated one by one when source feeds are used, but non-issues are omitted unless useful.
- Advisory feed review includes counts for feeds checked, records evaluated, action required, and needs verification.
- The plan uses existing repo security patterns where available.
- Regression tests include denied or blocked behavior when relevant.
- Remaining uncertainty is listed as missing evidence, not hidden inside the recommendation.

## Conversion

Read `paladin_conversion`, `paladin_action_destination`, and `paladin_custom_action` from `PALADIN.md`.

`paladin_conversion` is the local action contract. Do not ask before creating or updating an item unless `paladin_conversion` is exactly `ask_first`.

| Value | Behavior |
| --- | --- |
| `create_review_task` | Create or update a concise mitigation item without asking when destination tools and required config are available |
| `ask_first` | Ask before creating or updating a mitigation item |
| `report_only` | Do not create tasks; return the mitigation plan only |

If the field is missing, treat it as `create_review_task`. Create or update a task only after the finding and fix plan are specific enough to act on.

Default `paladin_action_destination` to `one_horizon`. Supported destinations are `one_horizon`, `linear`, `jira`, `email`, and `custom`. For non-One Horizon destinations, follow `paladin_custom_action`; use matching tools when available, otherwise return a ready-to-send issue, ticket, email, or structured handoff.

If `create_review_task` is set but destination tools or required config are unavailable, return the mitigation plan and state what prevented item creation. Do not ask for permission as a fallback.
