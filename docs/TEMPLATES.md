# Workflow Templates Documentation

This folder contains the reusable workflow templates available in this hub.

## CI Templates

| File | Description | Inputs |
|---|---|---|
| `ci/python-test.yml` | Run pytest for a Python project | `python-version`, `working-directory` |
| `ci/node-test.yml` | Build & test a Node.js project | `node-version`, `working-directory`, `package-manager` |

## CD Templates

| File | Description | Inputs |
|---|---|---|
| `cd/deploy-ssh.yml` | Deploy via SSH + Docker Compose to a VPS | `host`, `user`, `deploy-path`, `compose-file` |

## Automation Templates

| File | Description |
|---|---|
| `automation/auto-label-pr.yml` | Auto-label PRs based on `.github/labeler.yml` |

---

More templates coming soon. To add one, open a PR with:
- The `.yml` file in the correct category folder
- An entry in this table
