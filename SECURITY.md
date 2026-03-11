# Security Policy

## Supported Versions

| Version | Supported          |
|---------|--------------------|
| v1.x    | :white_check_mark: |
| < v1.0  | :x:                |

## Reporting a Vulnerability

If you discover a security vulnerability in this project, please report it responsibly.

**Do NOT open a public issue.** Instead, use one of the following methods:

1. **GitHub Private Vulnerability Reporting** — click the "Report a vulnerability" button in the [Security tab](../../security/advisories/new) of this repository.
2. **Email** — send details to the repository maintainer with subject line: `[SECURITY] workflow-hub vulnerability report`.

### What to include

- Description of the vulnerability
- Steps to reproduce
- Potential impact
- Suggested fix (if any)

### Response timeline

| Action                          | Target     |
|---------------------------------|------------|
| Acknowledge receipt             | 48 hours   |
| Initial assessment              | 5 days     |
| Fix and release (if confirmed)  | 30 days    |

We will credit reporters in the release notes unless anonymity is requested.

## Security Measures in This Repository

- All workflows declare **minimum required permissions**
- **Gitleaks** scans the full commit history for secrets
- **Dependency Review** blocks PRs introducing high-severity CVEs
- **Dependabot** keeps all GitHub Actions versions up to date
- **OSSF Scorecard** monitors security best practices
- **CodeQL** performs static analysis (SAST) on supported languages
- **Trivy** scans container images for known vulnerabilities
- Pre-commit hooks detect secrets and large files before push
- No third-party secrets are stored — only `GITHUB_TOKEN` is used
