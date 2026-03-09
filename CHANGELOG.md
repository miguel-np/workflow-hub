# Changelog

## [1.1.0] - 2026-03-09

### Added

- Workflow `pr-size.yml`: etiquetas XS/S/M/L/XL por líneas cambiadas
- Workflow `todo-to-issue.yml`: convierte TODO/FIXME en issues al push a main
- Workflow `lint-workflows.yml`: validación de sintaxis con actionlint (reviewdog)
- Pre-commit hook `check-added-large-files` (max 500KB)
- Labels `pinned` y `security` excluidos del stale bot
- Label `actions/**` en labeler.yml

### Fixed

- `pr-checks.yml`: unificado validate-title y check-description en un solo job (ahorra 1 runner)
- `security.yml`: añadido `security-events: write` para Gitleaks y condición `pull_request` en dependency-review
- `lint-workflows.yml`: reemplazado `curl | bash` por action oficial reviewdog/action-actionlint
- `stale.yml`: añadido bloque `concurrency` para evitar solapamiento de ejecuciones
- `dependabot.yml`: corregido prefix de `ci` a `ci:` (Conventional Commits válido)
- `todo-to-issue.yml`: cambiado de `workflow_call` a trigger `push` standalone
- Eliminada `concurrency` redundante de workflows reusable (`pr-checks`, `pr-size`)

## [1.0.0] - 2026-03-09

### Added

- Reusable workflows: pr-checks, security, release, stale, dependency-auto-merge
- Composite actions: validate-pr-title, gitleaks-scan, changelog-gen, auto-label
- Issue templates: bug report, feature request
- Pull request template con checklist
- Dependabot configurado para GitHub Actions
- Pre-commit: trailing-whitespace, end-of-file-fixer, check-yaml, check-json, check-merge-conflict, detect-secrets, commitizen
- Auto-labeler con reglas por tipo de archivo

### Removed

- Templates genéricos de CI/CD (python-test, node-test, deploy-ssh)
- Estructura de directorios templates/ y docs/
