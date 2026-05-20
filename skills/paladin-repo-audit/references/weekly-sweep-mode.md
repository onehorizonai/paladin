# Weekly Sweep Mode

Use this reference for weekly security sweeps, recent PR review, dependency-change review, and security metrics.

## Contents

- Sweep workflow
- Stable values
- Output contract
- Final self-check

## Sweep Workflow

1. Review merged PRs from the requested period that touch auth, permissions, input handling, dependencies, infrastructure, secrets, logging, or external calls.
2. Check new dependency vulnerabilities and prioritize known-exploited or internet-facing exposure.
3. Read the configured source list and check whether components or dependencies match CISA KEV, OSV, GitHub advisories, NVD, or relevant vendor advisories.
4. Review open security findings and flag stale mitigations.
5. Sample one or two risky areas for deeper manual review.
6. Record recurring patterns and suggest checklist improvements.
7. Report period, PR counts, finding counts, known-exploited vulnerability review, management risks, and process improvements.

## Stable Values

- Priority: `P0`, `P1`, `P2`, `P3`
- Severity: `Medium`, `High`, `Critical`
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

- The output uses only stable values from this reference.
- The opening summary is understandable to a CEO with no security background.
- The sweep includes period, PR counts, finding counts, KEV/CVE review, risks, and process improvements.
- Current issue source lookup is recorded when recent vulnerability or dependency context affects the sweep.
- Advisory records are evaluated one by one when source feeds are used, but non-issues are omitted unless useful.
- Advisory feed review includes counts for feeds checked, records evaluated, action required, and needs verification.
- Management risks are evidence-backed and not generic checklist items.
