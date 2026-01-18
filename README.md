# Reconstruct Templates

Templates for Reconstruct CLI commands and rules for AI coding assistants.

## Overview

This repository contains the command and rule templates that are distributed via the [Reconstruct CLI](https://github.com/blkinnovate/reconstruct). These templates are automatically downloaded and installed when users run `reconstruct init`.

## Structure

```
reconstruct-templates/
├── commands/                          # Cursor command files
│   ├── recon-setup.md                 # One-time project setup + tutorial
│   ├── recon-manager.md               # Manager agent: plan work, create capsules
│   ├── recon-worker.md                # Worker agent: execute plans
│   └── recon-help.md                  # Tutorial and reference
├── rules/                             # Cursor rule files
│   ├── reconstruct-capsule-planning.md   # Plan format and creation
│   ├── reconstruct-state-machine.md      # Workflow state detection
│   └── reconstruct-recovery.md           # Error recovery guide
├── LICENSE
└── README.md
```

## Commands

| Command | Purpose |
|---------|---------|
| `/recon-setup` | Connect workspace to project (run once) |
| `/recon-manager` | Manager agent: create capsules, plan work |
| `/recon-worker` | Worker agent: execute plans with human approval |
| `/recon-help` | Tutorial and quick reference |

## Rules

| Rule | Purpose |
|------|---------|
| `reconstruct-capsule-planning` | Plan format template and creation workflow |
| `reconstruct-state-machine` | Detect workflow state and suggest next action |
| `reconstruct-recovery` | Error recovery steps for common issues |

## Typical Workflow

```
1. /recon-setup       → Connect to project (one-time)
2. /recon-manager     → Describe work, create capsule + plan
3. Open new chat
4. /recon-worker      → Execute the plan with approvals
5. Return to manager chat, say "done"
```

## Installation

Templates are automatically installed via the Reconstruct CLI:

```bash
npm install -g reconstruct-cli
reconstruct init
```

## Versioning

Templates are versioned independently from the CLI. Current version: **v0.4**

Each release includes:
- Command files with version headers
- Rule files with version headers
- A `templates-{version}.tar.gz` archive

## License

See [LICENSE](LICENSE) for license terms. This work is licensed under a custom license that permits personal and non-commercial use. Commercial use requires explicit permission.

## Links

- [Reconstruct Website](https://reconstruct.app)
- [Reconstruct CLI](https://github.com/blkinnovate/reconstruct-cli)
