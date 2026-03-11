# Changelog

## [1.1.0] - 2026-03-09

### Added

- Workflow `pr-size.yml`: XS/S/M/L/XL labels based on lines changed
- Workflow `todo-to-issue.yml`: converts TODO/FIXME comments to issues on push to main
- Workflow `lint-workflows.yml`: YAML syntax validation with actionlint via reviewdog
- Pre-commit hook `check-added-large-files` (max 500 KB)
- Labels `pinned` and `security` exempt from the stale bot
- Label rule `actions/**` added to `labeler.yml`

### Fixed

- `pr-checks.yml`: merged `validate-title` and `check-description` into a single job (saves 1 runner)
- `security.yml`: added `security-events: write` for Gitleaks and added `pull_request` condition to dependency-review
- `lint-workflows.yml`: replaced `curl | bash` install with the official `reviewdog/action-actionlint` action
- `stale.yml`: added `concurrency` block to prevent overlapping scheduled runs
- `dependabot.yml`: corrected commit prefix from `ci` to `ci:` (valid Conventional Commits format)
- `todo-to-issue.yml`: changed from `workflow_call` to a standalone `push` trigger
- Removed redundant `concurrency` blocks from reusable workflows (`pr-checks`, `pr-size`)

## [1.0.0] - 2026-03-09

### Added

- Reusable workflows: `pr-checks`, `security`, `release`, `stale`, `dependency-auto-merge`
- Composite actions: `validate-pr-title`, `gitleaks-scan`, `changelog-gen`, `auto-label`
- Issue templates: bug report, feature request
- Pull request template with checklist
- Dependabot configured for GitHub Actions
- Pre-commit hooks: `trailing-whitespace`, `end-of-file-fixer`, `check-yaml`, `check-json`, `check-merge-conflict`, `detect-secrets`, `commitizen`
- Auto-labeller with file-based rules

### Removed

- Generic CI/CD templates (python-test, node-test, deploy-ssh)
- `templates/` and `docs/` directory structure
