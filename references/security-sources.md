# Paladin Security Source List

Last reviewed: 2026-05-20.

Use this list when Paladin needs current vulnerability, advisory, public zero-day claim, or known-exploited context. Do not rely on model memory for "latest" security issues.

## Source Use Rules

1. Start from local evidence: package manifests, lockfiles, IaC files, framework versions, CVE/GHSA/OSV IDs, scanner output, and changed code.
2. Download or query only relevant advisory feeds. Do not browse every source for every review.
3. Prefer official, vendor, ecosystem, or standards sources over blogs, news, and social posts.
4. Treat third-party articles as leads only. Verify affected versions, exploit status, and mitigation against an official or vendor source before reporting.
5. Record the source, query, date checked, and result whenever current-issue lookup affects the review.
6. If network access or tooling is unavailable, mark the current advisory check as `not available` and say what local evidence was reviewed instead.
7. Do not download proof-of-concept exploit repositories, exploit payloads, weaponized code, live-target scanners, credential dumps, or exploit playbooks. Use advisory metadata only.
8. Do not run live-target scans, exploit probes, credential checks, or payload validation as part of source lookup.

## Active Advisory Feed Review

Use this workflow for weekly sweeps, repository audits, dependency changes, runtime or infrastructure changes, public zero-day claims, known-exploited issues, or requests to check recent exploits.

1. Read local inventory first: package names, versions, lockfiles, container images, runtimes, frameworks, operating system images, cloud services, and internet-facing components.
2. Download or query relevant machine-readable feeds from this file, such as CISA KEV JSON/CSV, OSV, GitHub advisories, NVD, or ecosystem/vendor feeds.
3. Normalize each advisory into:
   - ID: CVE, GHSA, OSV, vendor ID, or advisory URL
   - affected product or package
   - affected version range
   - fixed version or mitigation
   - exploit status: known exploited, public zero-day claim, advisory only, or unknown
   - source URL and date checked
4. Evaluate each potentially relevant advisory one by one against the repo inventory.
5. Assign one result:
   - `action required`: the repo appears affected, exposed, or missing the required mitigation
   - `already protected`: the exploit exists, but this repo uses a fixed or unaffected version, or an existing control blocks the issue
   - `not relevant`: no matching product, package, version, or reachable component exists
   - `needs verification`: local evidence is incomplete
6. Report only `action required` and `needs verification` by default.
7. Report `already protected` only when useful, such as when an exploit is high-profile but the repo already uses the fixed package version.
8. Omit `not relevant` items unless the user asks for a full audit trail.

## CEO-Readable Reporting

Keep the final report short and understandable to a non-security executive.

- Start with the decision: `Action required`, `No action needed`, or `Needs verification`.
- Say what it means in plain English before using technical labels.
- Define unavoidable terms on first use. Example: "CVE means a public vulnerability ID."
- Name the affected product, installed version, safe version or mitigation, and business impact.
- Avoid jargon-only labels. OWASP, CWE, ASVS, CVSS, KEV, GHSA, and OSV belong in details after the plain-English explanation.
- Do not include exploit payloads, exploit steps, or attacker instructions.
- Keep the executive section to 5 bullets or fewer.

Use this concise shape:

```text
Security check result: Action required / No action needed / Needs verification

- What changed:
- What Paladin checked:
- What matters:
- What to do next:
- Owner:
```

When advisory feeds are downloaded or queried, include this compact audit trail:

```text
Advisory feed review:
- Feeds checked:
- Records evaluated:
- Action required:
- Needs verification:
- Already protected, if useful:
```

## Core Standards

Use these for classification and control expectations.

| Source | URL | Use |
| --- | --- | --- |
| OWASP Top 10:2025 | https://owasp.org/Top10/2025/ | Broad web application risk categories |
| MITRE CWE Top 25 | https://cwe.mitre.org/top25/ | Common and impactful software weakness classes |
| OWASP ASVS | https://owasp.org/www-project-application-security-verification-standard/ | Application security verification controls |
| CVE Program | https://www.cve.org/ | Canonical CVE identifiers and CNA context |
| NVD CVE API | https://services.nvd.nist.gov/rest/json/cves/2.0 | CVE enrichment, CVSS, CPE, and reference metadata |

## Known Exploited And Active Risk Sources

Use these when reviewing dependencies, internet-facing services, weekly sweeps, CVEs, or infrastructure exposure.

| Source | URL | Use |
| --- | --- | --- |
| CISA Known Exploited Vulnerabilities Catalog | https://www.cisa.gov/known-exploited-vulnerabilities-catalog | Authoritative known-exploited vulnerability prioritization |
| CISA KEV JSON feed | https://www.cisa.gov/sites/default/files/feeds/known_exploited_vulnerabilities.json | Machine-readable KEV matching by CVE |
| CISA KEV CSV feed | https://www.cisa.gov/sites/default/files/csv/known_exploited_vulnerabilities.csv | Spreadsheet-friendly KEV matching by CVE |
| CWE Top 10 KEV Weaknesses | https://cwe.mitre.org/top25/ | Actively exploited weakness trend context linked from CWE Top 25 |

## Open Source Dependency Advisory Sources

Use these when package manifests, lockfiles, dependency bumps, or scanner findings are in scope.

| Source | URL | Use |
| --- | --- | --- |
| OSV.dev | https://osv.dev/ | Open source vulnerability lookup across ecosystems |
| OSV API docs | https://google.github.io/osv.dev/ | Version-aware package vulnerability queries |
| GitHub Advisory Database | https://github.com/advisories | GitHub-reviewed advisories, GHSAs, CVEs, package/ecosystem filters |
| GitHub Advisory GraphQL API | https://docs.github.com/en/graphql/reference/objects#securityadvisory | Structured advisory lookup when GitHub tooling is available |
| Go Vulnerability Database | https://pkg.go.dev/vuln/list | Go module vulnerabilities |
| RustSec Advisory Database | https://rustsec.org/advisories/ | Rust crate advisories |
| PyPI Security | https://pypi.org/security/ | Python package security reporting and ecosystem entry point |

## Vendor And Framework Sources

Use official vendor or framework advisory pages when a repo clearly depends on that product. Prefer exact package names and version ranges over broad searches.

Examples:

- Node.js security releases: https://nodejs.org/en/blog/vulnerability
- Django security release archive: https://docs.djangoproject.com/en/stable/releases/security/
- Ruby on Rails security policy: https://rubyonrails.org/security
- Ruby on Rails security announcements: https://discuss.rubyonrails.org/c/security-announcements/9
- Kubernetes security announcements: https://kubernetes.io/docs/reference/issues-security/official-cve-feed/
- Docker security announcements: https://docs.docker.com/security/security-announcements/

## Query Pattern

Use exact identifiers first:

```text
CVE-YYYY-NNNN
GHSA-xxxx-yyyy-zzzz
OSV-YYYY-NNN
CWE-NNN
package_name ecosystem version
vendor product version
```

For dependency review:

1. Identify ecosystem and package names from the manifest and lockfile.
2. Query OSV or ecosystem-specific advisory sources by package and version.
3. Query GitHub Advisory Database for GHSA/CVE matches.
4. Check CISA KEV by CVE when a CVE exists.
5. Confirm affected versions and fixed versions before recommending an upgrade.

For weekly sweeps:

1. Check CISA KEV for newly added CVEs relevant to detected dependencies, frameworks, images, and infrastructure.
2. Check OSV/GitHub advisories for changed dependencies and lockfiles.
3. Check vendor sources for internet-facing frameworks, runtimes, identity providers, storage, CI/CD, and container platforms.
4. Report only relevant matches with affected component, installed version, fixed version or mitigation, and source URL.

## Output Snippet

Include this section when current-issue lookup changes the result:

```text
Current issue sources checked:
- Source:
- Query:
- Date checked:
- Result:
```

If nothing relevant was found:

```text
Current issue sources checked:
- Source:
- Query:
- Date checked:
- Result: no relevant match found
```
