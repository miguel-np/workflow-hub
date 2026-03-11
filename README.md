# workflow-hub

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Conventional Commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-yellow.svg)](https://conventionalcommits.org)
[![OSSF Scorecard](https://img.shields.io/badge/OSSF-Scorecard-blue.svg)](https://scorecard.dev)

> Central repository of reusable workflows, composite actions, and shared GitHub configuration for the organization.

---

## Table of Contents

- [Overview](#overview)
- [Repository Structure](#repository-structure)
- [Automation Layers](#automation-layers)
- [Usage](#usage)
  - [PR Checks](#pr-checks)
  - [Security Scan](#security-scan)
  - [Automated Releases](#automated-releases)
  - [Dependabot Auto-merge](#dependabot-auto-merge)
  - [CodeQL Analysis](#codeql-analysis)
  - [OSSF Scorecard](#ossf-scorecard)
- [Local Development](#local-development)
- [Versioning and Publishing Tags](#versioning-and-publishing-tags)
- [Commit Convention](#commit-convention)
- [Security](#security)

---

## Overview

This repository centralises all CI/CD automation for the organization. Consumer repositories call the reusable workflows via `workflow_call` — they do not need to duplicate any pipeline logic.

**Estimated GitHub Actions consumption:** ~110 min / month out of 2,000 available (~5.5%).

---

## Repository Structure

```
workflow-hub/
├── .github/
│   ├── workflows/                       # Reusable workflows (what runs)
│   │   ├── pr-checks.yml                #   PR: title, description & labels
│   │   ├── pr-size.yml                  #   PR: XS/S/M/L/XL size labels
│   │   ├── security.yml                 #   PR: Gitleaks + Dependency Review
│   │   ├── release.yml                  #   Merge: CHANGELOG + GitHub Release
│   │   ├── dependency-auto-merge.yml    #   Auto-merge safe Dependabot PRs
│   │   ├── stale.yml                    #   Nightly: close inactive issues/PRs
│   │   ├── todo-to-issue.yml            #   Push: TODOs → GitHub issues
│   │   ├── lint-workflows.yml           #   CI: YAML syntax validation
│   │   ├── enforce-commits.yml          #   PR: Conventional Commits lint
│   │   ├── auto-assign.yml              #   PR: auto-assign author
│   │   ├── branch-cleanup.yml           #   PR merged: delete source branch
│   │   ├── greet.yml                    #   First-time contributor welcome
│   │   ├── lock-closed.yml              #   Nightly: lock closed threads
│   │   ├── codeql.yml                   #   Scheduled: CodeQL SAST
│   │   └── scorecard.yml               #   Scheduled: OSSF Scorecard
│   ├── ISSUE_TEMPLATE/                  # Issue forms
│   │   ├── bug_report.yml
│   │   └── feature_request.yml
│   ├── PULL_REQUEST_TEMPLATE.md         # PR description template
│   ├── dependabot.yml                   # Automated Actions version updates
│   └── labeler.yml                      # File-based auto-labelling rules
├── actions/                             # Composite actions (how it runs)
│   ├── validate-pr-title/
│   ├── gitleaks-scan/
│   ├── changelog-gen/
│   └── auto-label/
├── .pre-commit-config.yaml              # Local validation hooks
└── .gitignore
```

---

## Automation Layers

| Layer | Component | Automates | Est. min |
|---|---|---|---|
| Local | pre-commit | Formatting, secrets, commit messages, large files | 0 |
| PR | `pr-checks.yml` | CC title, non-empty description, file-based labels | ~1 |
| PR | `pr-size.yml` | XS/S/M/L/XL labels by lines changed | ~0.5 |
| PR | `security.yml` | Gitleaks secret scan + Dependency Review | ~1 |
| PR | `enforce-commits.yml` | Lint all commit messages in the PR | ~1 |
| PR | `auto-assign.yml` | Assign PR to its author | ~0.5 |
| Auto | Dependabot | Automated version-bump PRs for Actions | 0 |
| Auto | `dependency-auto-merge.yml` | Merge patch/minor updates without high-severity CVEs | ~1 |
| Merge | `release.yml` | CHANGELOG, semver tag, GitHub Release | ~2 |
| Merge | `branch-cleanup.yml` | Delete the source branch after merge | ~0.5 |
| Push | `todo-to-issue.yml` | Convert TODO/FIXME comments into GitHub issues | ~0.5 |
| Nightly | `stale.yml` | Warn and close issues/PRs inactive for 60+ days | ~0.5 |
| Nightly | `lock-closed.yml` | Lock closed threads after 30 days | ~0.5 |
| Nightly | `greet.yml` | Welcome first-time contributors | ~0.5 |
| Scheduled | `codeql.yml` | CodeQL static analysis (SAST) | ~5 |
| Scheduled | `scorecard.yml` | OSSF Scorecard security posture | ~1 |
| CI | `lint-workflows.yml` | Validate workflow YAML with actionlint | ~0.5 |

---

## Usage

Replace `ORG` with your organization name in all examples below.

> **Important:** Define `concurrency` in the **caller** workflow, not in the reusable workflows. This prevents stale runs from consuming minutes.

### PR Checks

Validates PR title (Conventional Commits), description, and applies file-based labels.

```yaml
# .github/workflows/pr.yml
name: Pull Request

on:
  pull_request:
    branches: [main]

concurrency:
  group: ${{ github.workflow }}-${{ github.event.pull_request.number }}
  cancel-in-progress: true

jobs:
  checks:
    uses: ORG/workflow-hub/.github/workflows/pr-checks.yml@v1

  size:
    uses: ORG/workflow-hub/.github/workflows/pr-size.yml@v1

  security:
    uses: ORG/workflow-hub/.github/workflows/security.yml@v1

  enforce-commits:
    uses: ORG/workflow-hub/.github/workflows/enforce-commits.yml@v1

  auto-assign:
    uses: ORG/workflow-hub/.github/workflows/auto-assign.yml@v1
```

### Security Scan

Runs Gitleaks on the full commit history and Dependency Review on PRs.

```yaml
jobs:
  security:
    uses: ORG/workflow-hub/.github/workflows/security.yml@v1
```

### Automated Releases

Creates a versioned GitHub Release and updates `CHANGELOG.md` on every merge to `main`.

```yaml
# .github/workflows/release.yml
name: Release

on:
  push:
    branches: [main]

jobs:
  release:
    uses: ORG/workflow-hub/.github/workflows/release.yml@v1
    with:
      release-type: node   # node | python | go | rust | simple | ...
```

### Dependabot Auto-merge

Automatically merges patch and minor Dependabot PRs that pass all checks.

```yaml
# .github/workflows/dependabot.yml
name: Dependabot Auto-merge

on: pull_request

jobs:
  auto-merge:
    uses: ORG/workflow-hub/.github/workflows/dependency-auto-merge.yml@v1
```

### CodeQL Analysis

Performs SAST on the specified language. Run on a schedule or on every push.

```yaml
# .github/workflows/codeql.yml
name: CodeQL

on:
  push:
    branches: [main]
  schedule:
    - cron: '0 3 * * 1'   # every Monday at 03:00 UTC

jobs:
  codeql:
    uses: ORG/workflow-hub/.github/workflows/codeql.yml@v1
    with:
      language: javascript-typescript   # see supported languages in codeql.yml
```

### OSSF Scorecard

Monitors the repository's security posture and publishes results to the Security tab.

```yaml
# .github/workflows/scorecard.yml
name: Scorecard

on:
  push:
    branches: [main]
  schedule:
    - cron: '0 4 * * 1'

jobs:
  scorecard:
    uses: ORG/workflow-hub/.github/workflows/scorecard.yml@v1
```

---

## Local Development

### Prerequisites

- Python 3.9+
- [pre-commit](https://pre-commit.com/) 3.x

### Setup

```bash
pip install pre-commit
pre-commit install                        # runs on every git commit
pre-commit install --hook-type commit-msg # validates commit messages
```

### Verify

```bash
pre-commit run --all-files
```

All checks should pass on a clean checkout.

---

## Versioning and Publishing Tags

Workflows are referenced by tag (`@v1`). To publish a new version after a release, update the floating major tag:

```bash
# Create a full version tag (done automatically by release.yml)
git tag v1.1.0
git push origin v1.1.0

# Move the floating major tag to the new release
git tag -f v1 v1.1.0
git push -f origin v1
```

Consumer repositories use the floating tag (`@v1`) so they benefit from non-breaking updates automatically.

---

## Commit Convention

All commits must follow [Conventional Commits v1.0.0](https://www.conventionalcommits.org/en/v1.0.0/):

```
<type>(<scope>): <short summary>
```

| Type | Version bump | Example |
|---|---|---|
| `feat` | MINOR | `feat(pr-checks): add description length validation` |
| `fix` | PATCH | `fix(security): add missing permission` |
| `feat!` / `BREAKING CHANGE` | MAJOR | `feat!: rename release-type input` |
| `docs`, `chore`, `refactor`, `ci`, `test` | No release | `docs: update usage examples` |

---

## Security

- All workflows declare **minimum required** `permissions`.
- `security-events: write` is scoped only to workflows that report to the Security tab.
- `dependency-review` runs exclusively on pull request events.
- Timeouts are set on every job (10 min default, 30 min for CodeQL).
- Dependabot keeps all Actions versions up to date weekly.
- The stale bot exempts issues and PRs labelled `pinned` or `security`.
- Pre-commit blocks files larger than 500 KB and detects secrets before push.
- No third-party secrets are stored — only `GITHUB_TOKEN` is used.

For vulnerability reports, see [SECURITY.md](SECURITY.md).

---

*For contribution guidelines, see [CONTRIBUTING.md](CONTRIBUTING.md).*
