# Contributing to workflow-hub

> Thank you for investing your time in contributing to this project. Every contribution — no matter how small — is valued and appreciated.

---

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Types of Contributions](#types-of-contributions)
- [Before You Start](#before-you-start)
- [Setting Up Your Environment](#setting-up-your-environment)
- [Development Workflow](#development-workflow)
- [Commit Convention](#commit-convention)
- [Pull Request Process](#pull-request-process)
- [Code Review Guidelines](#code-review-guidelines)
- [Adding a New Reusable Workflow](#adding-a-new-reusable-workflow)
- [Adding a New Composite Action](#adding-a-new-composite-action)
- [Versioning](#versioning)
- [Reporting Bugs](#reporting-bugs)
- [Requesting Features](#requesting-features)
- [Getting Help](#getting-help)
- [Recognition](#recognition)

---

## Code of Conduct

This project adheres to the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md). By participating, you are expected to uphold this standard. Please report unacceptable behaviour to the maintainers.

---

## Types of Contributions

We welcome all of the following:

| Contribution | Description |
|---|---|
| Bug reports | Identify and document unexpected behaviour |
| Feature requests | Propose new workflows, actions, or capabilities |
| Documentation | Improve clarity, fix typos, translate content |
| Code fixes | Correct bugs or security issues |
| New workflows | Add reusable workflows following project standards |
| New actions | Add composite actions following project standards |
| Reviews | Review open pull requests |

> **First-time contributor?** Look for issues labelled [`good first issue`](../../issues?q=label%3A%22good+first+issue%22) — they are scoped to be approachable without deep knowledge of the codebase.

---

## Before You Start

- Search [open issues](../../issues) and [pull requests](../../pulls) to avoid duplicating effort.
- For significant changes, open an issue first to discuss the approach before writing code.
- For security vulnerabilities, follow the [Security Policy](SECURITY.md) — **do not open a public issue**.

---

## Setting Up Your Environment

### Prerequisites

| Tool | Minimum version | Purpose |
|---|---|---|
| Git | 2.39+ | Version control |
| Python | 3.9+ | Required by pre-commit |
| [pre-commit](https://pre-commit.com/) | 3.x | Local hook runner |

### Initial setup

**1. Fork the repository**

On GitHub, click **Fork** (top-right corner of the repository page) to create your own copy.

**2. Clone your fork**

```bash
git clone https://github.com/<your-username>/workflow-hub.git
cd workflow-hub
```

**3. Add the upstream remote**

```bash
git remote add upstream https://github.com/<org>/workflow-hub.git
```

**4. Install pre-commit hooks**

```bash
pip install pre-commit
pre-commit install                        # runs on every commit
pre-commit install --hook-type commit-msg # validates commit messages
```

**5. Verify hooks are working**

```bash
pre-commit run --all-files
```

All checks should pass on a clean checkout. If any fail, please open an issue.

---

## Development Workflow

```
main (protected)
  └── feat/my-feature      ← your working branch
        └── (PR) ──────────► main
```

1. **Sync with upstream** before starting any work:
   ```bash
   git fetch upstream
   git checkout main
   git merge upstream/main
   ```

2. **Create a branch** from `main` using the naming convention below:
   ```bash
   git checkout -b <type>/<short-description>
   # Examples:
   git checkout -b feat/pr-size-workflow
   git checkout -b fix/gitleaks-permissions
   git checkout -b docs/contributing-guide
   ```

3. **Make your changes.** Keep commits small and focused.

4. **Run local checks** before pushing:
   ```bash
   pre-commit run --all-files
   ```

5. **Push your branch** and open a Pull Request.

### Branch naming convention

| Prefix | Use for |
|---|---|
| `feat/` | New workflow, action, or feature |
| `fix/` | Bug fix or correction |
| `docs/` | Documentation only |
| `chore/` | Maintenance, dependencies, config |
| `refactor/` | Code restructure without behaviour change |
| `ci/` | CI/CD pipeline changes |

> Direct pushes to `main` are blocked. All changes must go through a reviewed pull request.

---

## Commit Convention

All commits **must** follow [Conventional Commits v1.0.0](https://www.conventionalcommits.org/en/v1.0.0/).

### Format

```
<type>(<scope>): <short summary>
│       │              │
│       │              └─ Imperative, lowercase, no period at the end.
│       └─ Optional. Affected area: workflow name, action name, etc.
└─ See types table below.
```

### Types

| Type | Use for |
|---|---|
| `feat` | New workflow, action, or user-facing capability |
| `fix` | Bug fix or correction |
| `docs` | Documentation only |
| `chore` | Maintenance, dependency updates, config |
| `refactor` | Code restructure without behaviour change |
| `ci` | CI/CD configuration changes |
| `test` | Adding or updating tests |
| `perf` | Performance improvements |
| `revert` | Reverting a previous commit |

### Examples

```
feat(pr-checks): add description length validation
fix(gitleaks-scan): add missing security-events permission
docs(contributing): document branch naming convention
chore(deps): bump actions/checkout from 4.1.0 to 4.2.0
ci: enable actionlint on pull_request event
```

### Breaking changes

Append a `!` after the type/scope and add a `BREAKING CHANGE:` footer:

```
feat(release)!: change input name from `type` to `release-type`

BREAKING CHANGE: callers must rename the `type` input to `release-type`.
```

### Rules enforced by pre-commit

- Subject line must be ≤ 72 characters.
- `wip:`, `WIP:`, and `[WIP]` prefixes are **rejected** — squash work-in-progress commits before requesting review.
- The type must be one of the types listed above.

---

## Pull Request Process

### Before opening the PR

- [ ] Branch is rebased on top of the latest `main`.
- [ ] `pre-commit run --all-files` passes locally.
- [ ] PR title follows Conventional Commits format.
- [ ] `CHANGELOG.md` is updated under an `Unreleased` section (dependency-only PRs are exempt).
- [ ] `README.md` is updated if you added a workflow or action.

### Opening the PR

1. Push your branch:
   ```bash
   git push origin <your-branch>
   ```
2. On GitHub, navigate to the repository. A **"Compare & pull request"** banner will appear — click it.
3. Fill in all sections of the pull request template. Do not leave any section blank.
4. Assign yourself as the author.
5. Apply the relevant label(s) (e.g., `feat`, `fix`, `docs`). The auto-labeler will also apply labels based on the files changed.
6. If the PR is a work in progress, set it as a **Draft** pull request.

### After opening the PR

- All CI checks must pass before a review is requested.
- At least **one maintainer approval** is required to merge.
- Address all review comments. Mark conversations as resolved only after they have been addressed.
- Do not force-push after a review has started unless explicitly asked by a reviewer.

### Merge strategy

| Scenario | Strategy |
|---|---|
| Single-commit PR | Merge commit or squash |
| Multi-commit PR with clean history | Merge commit |
| Multi-commit PR with messy history | Squash and merge |

Rebase merges are not used in this repository.

---

## Code Review Guidelines

### For authors

- Keep PRs small and focused. A PR that does one thing is easier to review and faster to merge.
- Respond to all comments, even if only to acknowledge.
- Do not take review feedback personally — it is about the code, not the contributor.

### For reviewers

- Review within **5 business days** when possible.
- Be constructive and specific. Prefer "Consider extracting this into a separate step for reusability" over "This is wrong."
- Distinguish between blocking issues and optional suggestions (prefix suggestions with `nit:` or `optional:`).
- Approve only when you are confident the change is correct, safe, and consistent with project standards.

---

## Adding a New Reusable Workflow

All reusable workflows live in `.github/workflows/` and use `workflow_call` as their trigger.

### Checklist

- [ ] Trigger is `workflow_call` only (no standalone triggers unless intentional).
- [ ] All `inputs` have a `description`, `type`, and `default` where applicable.
- [ ] `permissions` block declares only the minimum required scopes.
- [ ] Every job has `timeout-minutes` set.
- [ ] No hardcoded secrets — use `secrets.GITHUB_TOKEN` or inherited secrets.
- [ ] `README.md` is updated with usage documentation.
- [ ] `CHANGELOG.md` is updated.

### Template

```yaml
name: My Workflow

on:
  workflow_call:
    inputs:
      my-input:
        description: 'Human-readable description of what this input controls.'
        type: string
        required: false
        default: 'default-value'
    secrets:
      token:
        description: 'GitHub token passed by the caller.'
        required: false

permissions:
  contents: read   # declare only what is strictly necessary

jobs:
  my-job:
    name: Descriptive Job Name
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      # Add steps here
```

---

## Adding a New Composite Action

All composite actions live in `actions/<action-name>/`.

### Checklist

- [ ] Directory follows the `actions/<kebab-case-name>/` convention.
- [ ] `action.yml` uses `using: composite`.
- [ ] All `inputs` and `outputs` have `description` fields.
- [ ] Action pinned versions are consistent with the rest of the repository.
- [ ] `README.md` is updated with usage documentation.
- [ ] `CHANGELOG.md` is updated.

### Template

```yaml
name: 'My Action'
description: 'One-sentence description of what this action does.'
author: '<org>'

inputs:
  my-input:
    description: 'Human-readable description.'
    required: true

outputs:
  my-output:
    description: 'Human-readable description of the output.'
    value: ${{ steps.my-step.outputs.result }}

runs:
  using: composite
  steps:
    - name: Do something
      id: my-step
      shell: bash
      run: |
        echo "result=value" >> "$GITHUB_OUTPUT"
```

---

## Versioning

This project follows [Semantic Versioning 2.0.0](https://semver.org/):

| Version bump | Trigger |
|---|---|
| **MAJOR** (`v2.0.0`) | Breaking change in a workflow input/output or action interface |
| **MINOR** (`v1.1.0`) | New workflow or action added in a backwards-compatible way |
| **PATCH** (`v1.0.1`) | Bug fix or documentation update |

Releases are created automatically on merge to `main` by `release.yml`. You do not need to create tags manually.

---

## Reporting Bugs

1. Check [open issues](../../issues) to see if the bug has already been reported.
2. If not, open a new issue using the **Bug Report** template.
3. Include:
   - A clear, descriptive title.
   - Steps to reproduce the problem.
   - Expected vs. actual behaviour.
   - Relevant workflow run logs (redact any sensitive values).
   - The version (`v1.x.x`) where the issue was observed.

> For security vulnerabilities, follow the [Security Policy](SECURITY.md) instead of opening a public issue.

---

## Requesting Features

1. Check [open issues](../../issues) to see if the feature has already been requested.
2. If not, open a new issue using the **Feature Request** template.
3. Describe the problem you are trying to solve, not just the solution you have in mind.
4. If you plan to implement it yourself, mention that in the issue so a maintainer can assign it to you.

---

## Getting Help

| Channel | Use for |
|---|---|
| [Issues](../../issues) | Bug reports, feature requests |
| [Discussions](../../discussions) | Questions, ideas, general feedback |
| [SECURITY.md](SECURITY.md) | Reporting vulnerabilities |

---

## Recognition

All contributors are acknowledged in the release notes. Significant contributions may also be highlighted in the `README.md`.

We sincerely thank everyone who takes the time to improve this project.

---

*This document is based on best practices from the open-source community. Last updated: 2026-03-11.*
