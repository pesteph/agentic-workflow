# Security Policy

## Supported Versions

This project is a workflow template — it does not contain executable code that could have security vulnerabilities. Even so, Skills or workflow rules can contain weaknesses that lead to unsafe decisions in downstream projects (for example, a Skill that suggests insecure defaults).

We consider the **current `main` branch** to be the only supported version.

## Reporting a Vulnerability

**Please do NOT report security issues as a public GitHub Issue.**

Instead:

1. **Preferred: GitHub Private Vulnerability Reporting**
   - Go to [Security → Report a vulnerability](https://github.com/pesteph/agentic-workflow/security/advisories/new)
   - Describe the problem in as much detail as possible

2. **Alternative: Direct message**
   - Contact [@pesteph](https://github.com/pesteph) directly on GitHub

### What should be reported?

- Skills that recommend insecure patterns as best practice (for example hardcoded secrets, missing input validation)
- Workflow rules that lead to insecure defaults
- Documentation that guides users into insecure configurations
- Problems with the repo setup itself (for example workflow permissions, secret exposure)

### What happens then?

- **Acknowledgment** of receipt within a few days
- **Assessment** of severity and scope
- **Fix** is developed and reviewed privately
- **Disclosure** happens in a coordinated way after the fix release
- **Credit** for the reporter (if desired)

Thank you for helping keep this project secure!
