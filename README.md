# Reconstruct Templates

Templates for Reconstruct CLI commands and rules for AI coding assistants.

## Overview

This repository contains the command and rule templates that are distributed via the [Reconstruct CLI](https://github.com/blkinnovate/reconstruct). These templates are automatically downloaded and installed when users run `reconstruct init`.

## Structure

```
reconstruct-templates/
├── commands/          # Cursor command files
│   ├── recon-init.md
│   ├── recon-implement.md
│   └── recon-sync.md
├── rules/             # Cursor rule files
│   └── reconstruct-task-planning.md
├── LICENSE
└── README.md
```

## Installation

Templates are automatically installed via the Reconstruct CLI:

```bash
npm install -g reconstruct-cli
reconstruct init
```

## Versioning

Templates are versioned independently from the CLI. Each release includes:
- Command files with version headers
- Rule files with version headers
- A `templates-{version}.tar.gz` archive

## License

See [LICENSE](LICENSE) for license terms. This work is licensed under a custom license that permits personal and non-commercial use. Commercial use requires explicit permission.

## Contributing

This repository is maintained as part of the Reconstruct project. For issues or feature requests, please contact the maintainers.

## Links

- [Reconstruct Website](https://reconstruct.app)
- [Reconstruct CLI](https://github.com/blkinnovate/reconstruct-cli)
- [Main Repository](https://github.com/blkinnovate/reconstruct)

