# Security Review Output Contract

Use this reference for broad Paladin security reviews that combine repo sweep, changed-code review, misuse-case analysis, and current advisory checks.

## Stable Values

- Security check result: `Action required`, `No action needed`, `Needs verification`
- Overall risk: `Low`, `Medium`, `High`, `Critical`
- Priority: `P0`, `P1`, `P2`, `P3`
- Severity: `Medium`, `High`, `Critical`
- Confidence: `High`
- Check status: `passed`, `failed`, `not applicable`, `not available`
- Advisory result: `action required`, `needs verification`, `already protected`, `not relevant`

## Report Output

Open with a CEO-readable summary. Keep it to 5 bullets or fewer. Define unavoidable technical terms before using acronyms.

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
Repository security review

Scope:
- Repo/components reviewed:
- Changed-code pass:
- High-risk areas sampled:
- Areas not reviewed:

Overall risk:
- Low / Medium / High / Critical
- Reason:

Misuse cases reviewed:
- Actor:
- Asset or privilege:
- Trust boundary:
- Attacker-controlled input:
- Source-to-sink path:
- Expected control:
- Test or automation coverage:

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

Findings or backlog items:
```

For each item:

```text
[P0/P1/P2/P3] [Medium/High/Critical] [Title]
- Category: OWASP / CWE / ASVS
- Evidence:
- Impact:
- Recommended mitigation:
- Required test or verification:
- Confidence: High
- Blocking now: yes/no
```

Close with:

```text
Next review focus:
- [one or two highest-value next checks]

Coverage note:
- This is a prioritized defensive review of observed evidence, not a penetration test or unknown zero-day assessment.
```

## Final Self-Check

Before finalizing, verify:

- The output uses only stable values from this reference.
- The opening summary is understandable to a CEO with no security background.
- Every item has repo evidence, impact, mitigation, and required test or verification.
- Every code finding has High confidence and a concrete source-to-sink or privilege-boundary failure.
- Trusted config, env vars, constants, client-only checks, and framework-protected APIs are not treated as attacker control without repo evidence.
- Current issue source lookup is recorded when recent vulnerability or dependency context affects the review.
- Advisory records are evaluated one by one when source feeds are used, but non-issues are omitted unless useful.
- Advisory feed review includes counts for feeds checked, records evaluated, action required, and needs verification.
- No generic checklist item is reported as a finding without observed evidence.
- Coverage limits are stated clearly.
- The report does not include exploit playbooks, payloads, or abuse-enabling instructions.
