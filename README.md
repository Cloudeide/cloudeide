<div align="center">

# CloudeIDE

**Deploy from your terminal — or let your AI coding agent do it.**

[![npm](https://img.shields.io/npm/v/cloudeide?color=0b0a09&label=npm)](https://www.npmjs.com/package/cloudeide)
[![MCP Registry](https://img.shields.io/badge/MCP%20Registry-com.cloudeide%2Fcloudeide-0b0a09)](https://registry.modelcontextprotocol.io/)
[![License](https://img.shields.io/badge/license-MIT-0b0a09)](LICENSE)

[Website](https://cloudeide.com) · [Quick start](docs/quickstart.md) · [CLI reference](docs/cli.md) · [MCP setup](docs/mcp.md) · [Troubleshooting](docs/troubleshooting.md)

</div>

---
cloudeide 

cloudeide is an AI-powered code editor built on the VS Code foundation.

CloudID gives developers a complete coding environment with an AI agent that can work directly with their projects and codebase.

What  cloudeide Can Do

- Write and edit code
- Create, modify, and manage files
- Understand project structure and codebases
- Find bugs and coding problems
- Fix errors and improve existing code
- Refactor and optimize code
- Add new features
- Work across multiple files
- Help developers complete coding tasks
- Use the terminal and developer tools
- Deploy projects when they are ready

CloudID is designed to bring AI coding agents directly into the code editor, helping developers build, edit, debug, and ship software faster.

CloudID — Your AI Agent Code Editor.

## Documentation

| Guide | What's in it |
|---|---|
| [Quick start](docs/quickstart.md) | First deploy, start to finish |
| [Installation](docs/installation.md) | Package managers, Node versions, upgrading, uninstalling |
| [CLI reference](docs/cli.md) | Every command, flag and exit code |
| [MCP setup](docs/mcp.md) | Cursor, Claude Code and Codex — plus all 13 tools |
| [Configuration](docs/configuration.md) | Config file, environment variables, precedence |
| [Authentication](docs/authentication.md) | Tokens, scopes, rotation, what to do if one leaks |
| [Examples](docs/examples.md) | CI pipelines, preview deploys, scripting against `--json` |
| [Troubleshooting](docs/troubleshooting.md) | Errors by symptom, and what each actually means |
| [FAQ](docs/faq.md) | Questions that come up repeatedly |

## Editor support

| Editor | Config written | Scope |
|---|---|---|
| **Cursor** | `.cursor/mcp.json` | Project, or `~/.cursor/mcp.json` with `--global` |
| **Claude Code** | `.mcp.json` | Project — commit it and your whole team gets the tools |
| **Codex** | `.codex/config.toml` | Project, or `~/.codex/config.toml` with `--global` |

Run `cloudeide doctor` afterwards. It checks each tool's configuration and tells you what's wrong rather than just failing.

## Scripting

Every command takes `--json`, which prints one JSON document on stdout and nothing else — no progress lines, no colour. Exit codes are specific (`3` not authenticated, `7` out of credits, `9` server unreachable), so a script can branch on the failure rather than on a string match.

```bash
URL=$(cloudeide deploy . --env production --json | jq -r '.liveUrl')
```

See [examples](docs/examples.md).

## Getting help

- **A bug, or something that behaves surprisingly** — [open an issue](https://github.com/cloudeide/cloudeide/issues)
- **A security vulnerability** — please do *not* open a public issue; see [SECURITY.md](SECURITY.md)
- **Account, billing or deployment questions** — the dashboard at [cloudeide.com](https://cloudeide.com)

## Contributing

Issues and pull requests are welcome. [CONTRIBUTING.md](CONTRIBUTING.md) covers how to report something usefully and what to expect from review.

## License

MIT — see [LICENSE](LICENSE).
