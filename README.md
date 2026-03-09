# Workflow Hub

A centralized library of reusable GitHub Actions workflows, templates, and best practices.

## Purpose

This repository serves as a personal/team hub for GitHub Actions workflows, designed to be referenced and reused across multiple projects. Every workflow here is tested, documented, and ready to use.

## Structure

```
workflow-hub/
├── .github/
│   └── workflows/          # Meta-workflows (CI for this repo itself)
├── docs/                   # Guides and documentation
├── templates/              # Reusable workflow templates
│   ├── ci/                 # Continuous Integration workflows
│   ├── cd/                 # Continuous Deployment workflows
│   ├── quality/            # Code quality & security checks
│   ├── notifications/      # Notifications and reporting
│   └── automation/         # Issue/PR automation workflows  
└── README.md
```

## Categories

| Category | Description |
|---|---|
| **CI** | Build, test, lint pipelines |
| **CD** | Deploy to cloud, containers, static sites |
| **Quality** | SonarQube, CodeQL, dependency audit |
| **Notifications** | Slack, email, GitHub Discussions alerts |
| **Automation** | Labelers, stale managers, PR reviewers |

## Usage

Reference any workflow template from your repository using `workflow_call`:

```yaml
jobs:
  build:
    uses: miguel-np/workflow-hub/.github/workflows/TEMPLATE_NAME.yml@main
    with:
      param: value
    secrets: inherit
```

## Contributing

1. Add your workflow under the appropriate `templates/` subdirectory.
2. Document it in `docs/`.
3. Test it via the meta-CI in `.github/workflows/`.

## License

MIT
