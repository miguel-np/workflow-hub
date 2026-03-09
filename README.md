# .github

Repositorio central de workflows, actions y configuración para la organización.

## Estructura

```
├── .github/
│   ├── workflows/                    ← Reusable workflows (qué se ejecuta)
│   │   ├── pr-checks.yml                PR: título, descripción, labels
│   │   ├── pr-size.yml                  PR: etiqueta XS/S/M/L/XL por tamaño
│   │   ├── security.yml                 PR: Gitleaks + Dependency Review
│   │   ├── release.yml                  Merge: CHANGELOG + GitHub Release
│   │   ├── dependency-auto-merge.yml    Auto-merge de Dependabot
│   │   ├── stale.yml                    Nightly: limpieza issues/PRs inactivos
│   │   ├── todo-to-issue.yml            Push: TODOs → issues automáticos
│   │   └── lint-workflows.yml           CI: validación de sintaxis YAML
│   ├── ISSUE_TEMPLATE/               ← Templates de issues
│   ├── PULL_REQUEST_TEMPLATE.md
│   ├── dependabot.yml                ← Actualización automática de Actions
│   └── labeler.yml                   ← Reglas de auto-etiquetado
├── actions/                          ← Composite actions (cómo se ejecuta)
│   ├── validate-pr-title/
│   ├── gitleaks-scan/
│   ├── changelog-gen/
│   └── auto-label/
└── .pre-commit-config.yaml           ← Hooks de validación local
```

## Uso

Cada repositorio hereda los workflows reusable con `workflow_call`:

```yaml
# .github/workflows/ci.yml
name: CI

on:
  pull_request:
    branches: [main]

concurrency:
  group: ${{ github.workflow }}-${{ github.event.pull_request.number }}
  cancel-in-progress: true

jobs:
  checks:
    uses: ORG/.github/.github/workflows/pr-checks.yml@v1

  size:
    uses: ORG/.github/.github/workflows/pr-size.yml@v1

  security:
    uses: ORG/.github/.github/workflows/security.yml@v1
```

Para releases automáticos en merge a `main`:

```yaml
# .github/workflows/release.yml
name: Release

on:
  push:
    branches: [main]

jobs:
  release:
    uses: ORG/.github/.github/workflows/release.yml@v1
    with:
      release-type: node  # node, python, simple, go, rust...
```

Para auto-merge de Dependabot:

```yaml
# .github/workflows/dependabot-merge.yml
name: Dependabot Auto-merge

on: pull_request

jobs:
  auto-merge:
    uses: ORG/.github/.github/workflows/dependency-auto-merge.yml@v1
```

> Reemplaza `ORG` con el nombre de tu organización.
> La concurrencia se define en el **caller**, no en los reusable workflows.

## Capas

| Capa | Componente | Automatiza | Min |
|------|------------|------------|-----|
| Local | Pre-commit | Formato, secretos, commits, archivos grandes | 0 |
| PR | pr-checks.yml | Título CC, descripción, labels por ficheros | ~1 |
| PR | pr-size.yml | Labels XS/S/M/L/XL por líneas cambiadas | ~0.5 |
| PR | security.yml | Gitleaks (historial) + Dependency Review | ~1 |
| Auto | Dependabot | PRs de actualización de deps y Actions | 0 |
| Auto | dependency-auto-merge.yml | Merge patch/minor sin CVEs | ~1 |
| Merge | release.yml | CHANGELOG, tag, GitHub Release | ~2 |
| Push | todo-to-issue.yml | Convierte TODO/FIXME en issues | ~0.5 |
| Nightly | stale.yml | Cierre issues/PRs >60 días inactivos | ~0.5 |
| CI | lint-workflows.yml | Validación sintaxis workflows con actionlint | ~0.5 |

Consumo estimado: **~110 min/mes** sobre 2.000 disponibles (~5.5%).

## Setup

### Pre-commit (local)

```bash
pip install pre-commit
pre-commit install
pre-commit install --hook-type commit-msg
```

### Versionado

Los workflows se referencian por tag (`@v1`). Para publicar:

```bash
git tag v1.0.0
git push origin v1.0.0
git tag -f v1 v1.0.0
git push -f origin v1
```

## Conventional Commits

Todos los commits siguen `tipo(scope): descripción`:

| Tipo | Incremento | Ejemplo |
|------|------------|---------|
| `feat` | MINOR | `feat(auth): add OAuth2 login` |
| `fix` | PATCH | `fix(api): handle null response` |
| `feat!` / BREAKING CHANGE | MAJOR | `feat!: change auth API` |
| `docs`, `chore`, `refactor`, `ci`, `test` | Sin release | `docs: update README` |

## Seguridad

- Permisos mínimos (`permissions`) declarados en cada workflow
- `security-events: write` para que Gitleaks reporte en la pestaña Security
- `dependency-review` solo se ejecuta en PRs (no en push)
- Concurrencia: se define en el caller, evita runs obsoletos
- Timeouts explícitos de 10 min en todos los jobs
- Dependabot mantiene las versiones de Actions actualizadas
- Stale bot excluye issues con label `pinned` o `security`
- Pre-commit bloquea archivos >500KB y detecta secretos antes de push
- El código nunca sale de la infraestructura de GitHub
