# PR Review Output Contract

Use this reference when writing a Paladin PR review, posting review comments, or creating follow-up work.

## Contents

- Finding gate
- Stable values and finding limits
- Report output
- Finding output
- Automation outputs
- Final self-check

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

## Stable Values And Finding Limits

- Severity: `Medium`, `High`, `Critical`
- Blocking: `Yes`, `No`
- Decision: `Approved`, `Approved with non-blocking follow-up`, `Changes requested`, `Escalated for deeper security review`
- Check status: `passed`, `failed`, `not applicable`, `not available`
- Normal finding cap: 5, sorted by severity and exploitability
- Large review cap: 10 when the diff has more than 20 files, more than 1000 changed lines, or broad monorepo/root-level changes
- Overflow handling: never drop a Critical or High finding to keep a Medium finding. If valid findings remain past the cap, add an `Overflow` note with counts by severity and the recommended second-pass scope.

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

If the cap is reached and valid findings remain:

```text
Overflow:
- Additional valid findings not shown:
- Severity mix:
- Recommended second-pass scope:
```

## Finding Output

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

- The output uses only stable values from this reference.
- The opening summary is understandable to a CEO with no security background.
- Every finding has evidence, attack scenario, impact, mitigation, required test, severity, and blocking status.
- No finding is blocked by existing auth, validation, escaping, parameterization, allowlists, or type constraints.
- Current advisory source lookup is recorded when recent vulnerability or dependency context affects the review.
- Advisory records are evaluated one by one when source feeds are used, but non-issues are omitted unless useful.
- Advisory feed review includes counts for feeds checked, records evaluated, action required, and needs verification.
- Overflow is explicit when a large review has more valid findings than the cap.
- The review does not include exploit playbooks or weaponized payloads.
- The decision follows from the findings.
