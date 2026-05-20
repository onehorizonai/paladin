---
name: paladin-assess
description: Paladin's security context dispatcher. Use when no specific Paladin skill is requested, when the user asks for a security review but the mode is unclear, or when Codex should inspect git state and route to PR review, repository audit, or vulnerability mitigation.
---

# Assess

Detect the current security-review context and route to the right Paladin skill.

## Protocol

1. Read `PALADIN.md` if it exists. Use its frontmatter for conversion defaults, `paladin_action_destination`, `paladin_custom_action`, and the `paladin_source_list` path.
2. Run the checks below in order. Stop at the first clear match.
3. Announce what was detected in 2-3 lines.
4. Route immediately unless the request is genuinely ambiguous.

## Routing Contract

Use exactly one route:

| Route | Use when |
| --- | --- |
| `paladin-setup` | The user asks to set up Paladin, configure Paladin, create or update `PALADIN.md`, choose an action destination, or initialize repo config |
| `paladin-mitigate` | The user provides a CVE, CWE, dependency alert, scanner result, audit finding, or vulnerability description |
| `paladin-repo-audit` | The user asks for a repo audit, weekly sweep, recently merged PR review, metrics, checklist, baseline review, or backlog |
| `paladin-pr-review` | Changed files, staged files, an open PR, or a pasted diff needs security review |

If more than one route matches, use the first matching row in this table. Do not ask a question when the route is clear.

## Detection Checks

### Check A - Setup Or Configuration

If the user asks to set up Paladin, configure Paladin, create or update `PALADIN.md`, choose an action destination, or initialize repo config, route to `paladin-setup`.

Announce:

```text
Paladin sees a setup request.
Routing to repo setup so we can create or update PALADIN.md.
```

### Check B - Existing Finding

If the user provides a CVE, CWE, dependency alert, scanner finding, audit note, or vulnerability description, route to `paladin-mitigate`.

Announce:

```text
Paladin sees an existing security finding.
Routing to mitigation planning so we can turn it into concrete code changes and regression tests.
```

### Check C - Explicit Audit Or Weekly Sweep

If the user asks for a repo audit, checklist, baseline review, weekly sweep, recently merged PR review, security metrics, or security backlog, route to `paladin-repo-audit`.

### Check D - Changed Files

Run:

```bash
git status --porcelain
git diff --name-only
git diff --cached --name-only
git diff --stat
```

If there are changed or staged files, route to `paladin-pr-review`.

Announce:

```text
Paladin sees local code changes in [N] file(s).
Routing to PR security review and focusing on changed code plus required surrounding paths.
```

### Check E - Open PR

Run:

```bash
gh pr view --json number,title,url 2>/dev/null
```

If a PR exists for the current branch, route to `paladin-pr-review`. If `gh` fails, skip silently.

### Check F - No Clear Context

Ask one concise question:

```text
Do you want setup, PR security review, repository audit, or mitigation planning for a known finding?
```

## Routing Rules

- Do not perform a full review inside this skill. Detect context, announce, and route.
- Do not check current advisory sources inside this skill. Pass the configured source-list path to the routed skill.
- Do not reveal scratch reasoning. Report the detected signal and chosen route only.
