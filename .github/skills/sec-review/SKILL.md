---
name: sec-review
description: Conducts a security review of a pull request. Analyzes vulnerabilities, injection risks, auth issues, and other weaknesses. Use this Skill with a PR number or file paths.
---

# Security-Review

You perform an in-depth security review of a pull request or specified files.

## Execution

**Delegate** the security review to a Sub-Agent. Give it the complete Skill instructions, the scope, and intentional design choices (from architecture documentation or the concept). Show the user the complete result.

## Approach

### 1. Attack surface analysis

- Identify all places where external input is processed
- Check data flows from input to processing/storage
- Identify trust boundaries
- **Data-flow tracing**: For each input point — trace the complete data flow from the source to the usage. Is the input escaped, sanitized, or validated before it reaches SQL/HTML/Shell/Templates/database queries?

### 2. Vulnerability review

Check systematically for (with CWE reference):

- **Injection** — SQL (CWE-89), Command (CWE-78), XSS (CWE-79), SSRF (CWE-918), Template Injection (CWE-1336)
- **Authentication & authorization** — Missing checks (CWE-862), Privilege Escalation (CWE-863)
- **Data leaks** — Sensitive data in logs, error messages, responses (CWE-200, CWE-532)
- **Cryptography** — Weak algorithms, hardcoded keys/secrets (CWE-327, CWE-798)
- **Deserialization** — Unsafe deserialization of external data (CWE-502)
- **Dependencies** — Known vulnerabilities in used packages (CWE-1395), supply-chain attacks through compromised dependencies (CWE-1357) → see the dedicated section *Dependency analysis*
- **Configuration** — Debug modes, open endpoints, missing security headers (CWE-489, CWE-16)
- **Race conditions** — TOCTOU, missing synchronization in critical operations (CWE-367)

Reference framework: **OWASP Top 10 (2021)**

### 2a. Dependency analysis

Dedicated review of package dependencies for known vulnerabilities (CWE-1395) and supply-chain risks (CWE-1357).

#### Package manager detection

Identify the package managers in use based on manifest files:

| Ecosystem | Manifest files |
|-----------|----------------|
| NPM / Yarn / pnpm | `package.json`, `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml` |
| NuGet | `*.csproj`, `packages.config`, `Directory.Packages.props` |
| Maven / Gradle | `pom.xml`, `build.gradle`, `build.gradle.kts` |

> This list is extensible (e.g. pip/Poetry, cargo, Go modules). Apply an analogous approach as needed.

#### Vulnerability check

**Preferred — CLI tools** (if terminal access is available):
- NPM: `npm audit --json`
- NuGet: `dotnet list package --vulnerable --include-transitive`
- Maven: `mvn org.owasp:dependency-check-maven:check` (OWASP Dependency-Check Plugin)

**Fallback — research-based** (if CLI is not available):
- Identify the most critical / most used packages from the manifest files
- Use `/research` to check them against public vulnerability databases (NVD, GitHub Advisory Database, npm audit API)

#### Transitive dependencies

Check not only direct but also transitive dependencies. Supply-chain attacks often affect transitive packages that are not listed directly in the manifest.

#### Severity mapping (CVSS-based)

| CVSS Score | Severity |
|------------|----------|
| ≥ 9.0 | **Critical** — Fix immediately, blocks merge |
| ≥ 7.0 | **High** — Fix before merge |
| ≥ 4.0 | **Medium** — Should be fixed |
| < 4.0 | **Low** — Improvement proposal |

### 3. Assessment

Each finding is assessed:
- **Critical** — Fix immediately, blocks merge
- **High** — Fix before merge
- **Medium** — Should be fixed
- **Low** — Improvement proposal

## What is NOT a finding

- Correctly used framework-native security mechanisms
- Standard library usage that matches its documented intended use
- Intentional design choices documented in the concept

## Dynamic context (recommended)

For an in-depth security review:
1. Identify the technologies and frameworks in use
2. Use `/research` to investigate current attack vectors for these technologies (e.g. "current SSRF patterns in Node.js", "known deserialization vulnerabilities in .NET")
3. Integrate the results into the vulnerability review

## Output Format

```
## Security-Review

### Summary
[Overall assessment: Critical/High/Medium/Low/Clean]

### Findings

#### [SEV-LEVEL] [Title]
**File:** [Path:Line]
**Category:** [e.g. Injection, Auth]
**Description:** [What is the problem?]
**Impact:** [What can an attacker do?]
**Recommendation:** [How to fix it?]

### Dependencies

| Package Manager | Packages (direct/transitive) | Findings |
|-------------|--------------------------|----------|
| [e.g. NPM] | [e.g. 42/187] | [Count by severity] |

#### [SEV-LEVEL] [CVE-ID]: [Package Name] [Version]
**Category:** Dependency
**CVSS:** [Score]
**Description:** [What is the vulnerability?]
**Affected Versions:** [Range]
**Fix Version:** [Recommended version]
**Recommendation:** [Upgrade path or workaround]

### Reviewed Areas
- [Area] ✅

## Workflow State (update in plan.md)
- Completed Skill: /sec-review
- Result: [1-2 sentences: overall assessment, number of findings by severity]
- Next Skill: /doc-review
- Context for next Skill: [PR number or file paths]
```

💡 Context maintenance: Consider context compaction for long output. Update plan.md first.

---

**Next step:** Run `/doc-review` to check the documentation.