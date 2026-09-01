<div align="center">

# CloudeIDE

**Deploy from your terminal — or let your AI coding agent do it.**

[![npm](https://img.shields.io/npm/v/cloudeide?color=0b0a09&label=npm)](https://www.npmjs.com/package/cloudeide)
[![MCP Registry](https://img.shields.io/badge/MCP%20Registry-com.cloudeide%2Fcloudeide-0b0a09)](https://registry.modelcontextprotocol.io/)
[![License](https://img.shields.io/badge/license-MIT-0b0a09)](LICENSE)

[Website](https://cloudeide.com) · [Quick start](docs/quickstart.md) · [CLI reference](docs/cli.md) · [MCP setup](docs/mcp.md) · [Troubleshooting](docs/troubleshooting.md)

</div>

---

## What this is

CloudeIDE is a cloud development environment that runs in a browser tab — editor, AI agent, sandboxed execution and deployment, with nothing to install.

This repository is the **public, developer-facing part**: the `cloudeide` command-line tool, its MCP server, and the documentation for both. It is what you need if you want to drive CloudeIDE from your own terminal, or give an AI coding agent the ability to build and ship on your behalf.

The web IDE itself lives at [cloudeide.com](https://cloudeide.com).

## Why a CLI and an MCP server

Two different problems, one binary:

- **The CLI** is for you. `cloudeide deploy .` builds the current directory and puts it on a public URL, without opening a browser.
- **The MCP server** is for your agent. Cursor, Claude Code and Codex can call the same operations directly — deploy, watch the build, read the log, roll back — instead of asking you to run commands and paste the output back.

The second one is the interesting half. An agent that can only write code has to hand the result to a human to ship. An agent with these tools closes the loop itself.

## Install

```bash
npm install -g cloudeide
```

`pnpm add -g cloudeide`, `yarn global add cloudeide` and `bun add -g cloudeide` all work too. Node 20 or newer.

## Quick start

```bash
cloudeide login          # paste an API token from Settings → API tokens
cloudeide deploy .       # build the current directory and publish it
```

That's the whole flow. `deploy` prints a live URL when it finishes.

To let an editor drive it instead:

```bash
cloudeide install cursor        # or: claude-code, codex
```

That writes the correct MCP config for that tool, in the right place, in its own format. It merges into whatever config is already there rather than overwriting it, and it **never writes your token into the file** — the config is safe to commit.

See the [quick start](docs/quickstart.md) for the longer version.

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
