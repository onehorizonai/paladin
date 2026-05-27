# Code Review Output Contract

Use this reference when writing a Paladin changed-code review, PR review, review comment, or follow-up task.

## Finding Gate

Only report a candidate when every gate passes:

| Gate | Requirement |
| --- | --- |
| Changed scope | Introduced or exposed by the diff, or unchanged code is required to prove exploitability |
| Reachability | A realistic actor can reach the vulnerable path |
| Attacker control | Untrusted input or privilege reaches the sensitive path; trusted config alone is not enough |
| Control failure | Existing auth, validation, escaping, parameterization, allowlists, framework behavior, or type constraints do not block it |
| Impact | Plausible confidentiality, integrity, availability, privilege, tenant, secret, or supply-chain impact |
| Actionability | There is a concrete mitigation and regression test |
| Severity | Medium, High, or Critical |
| Confidence | High, roughly 80% or better, with evidence-backed source-to-sink reasoning |

If any gate fails, omit the candidate. Do not include a "maybe" finding.

## Stable Values

- Severity: `Medium`, `High`, `Critical`
- Blocking: `Yes`, `No`
- Confidence: `High`
- Decision: `Approved`, `Approved with non-blocking follow-up`, `Changes requested`, `Escalated for deeper security review`
- Check status: `passed`, `failed`, `not applicable`, `not available`
- Normal finding cap: 5, sorted by severity and exploitability
- Large review cap: 10 when the diff has more than 20 files, more than 1000 changed lines, or broad monorepo/root-level changes

Never drop a Critical or High finding to keep a Medium finding. If valid findings remain past the cap, add an `Overflow` note with counts by severity and the recommended second-pass scope.

## Report Output

Open with a CEO-readable summary before the detailed contract. Keep it to 5 bullets or fewer. Define unavoidable terms in plain English before using acronyms. If there are no findings, say `No action needed` and write `Findings: None`.

```text
Security check result: Action required / No action needed / Needs verification

- What changed:
- What Paladin checked:
- What matters:
- What to do next:
- Owner:
```

Then use:

```text
Security review summary

Scope reviewed:
- Files/components:
- Entry points:
- Data or privilege affected:

Risk classification:
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
- [file:line plus concrete source-to-sink behavior]

Exploit scenario:
- [bounded defensive scenario]

Impact:
- [who or what is affected]

Recommended mitigation:
- [smallest concrete fix]

Required test:
- [regression test that proves the control]

Confidence:
- High

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

## Final Self-Check

Before finalizing, verify:

- The output uses only stable values from this reference.
- The opening summary is understandable to a CEO with no security background.
- Every finding has evidence, attack scenario, impact, mitigation, required test, severity, and blocking status.
- Every finding has High confidence and a concrete source-to-sink or privilege-boundary failure.
- Trusted config, env vars, constants, client-only checks, and framework-protected APIs are not treated as attacker control without repo evidence.
- No finding is blocked by existing auth, validation, escaping, parameterization, allowlists, framework behavior, or type constraints.
- Current advisory source lookup is recorded when recent vulnerability or dependency context affects the review.
- Advisory records are evaluated one by one when source feeds are used, but non-issues are omitted unless useful.
- Advisory feed review includes counts for feeds checked, records evaluated, action required, and needs verification.
- Overflow is explicit when a large review has more valid findings than the cap.
- The review does not include exploit playbooks or weaponized payloads.
- The decision follows from the findings.
