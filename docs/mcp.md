# MCP setup

CloudeIDE ships an [MCP](https://modelcontextprotocol.io/) server, so an AI coding agent can deploy, watch builds, read logs and roll back on its own — instead of writing commands for you to run and paste back.

The server is part of the `cloudeide` package. There is nothing else to install.

## Registry listing

CloudeIDE is published to the official MCP Registry as:

```
com.cloudeide/cloudeide
```

The namespace is verified by DNS on `cloudeide.com`, so a client can tell an official listing from an impersonation.

---

## Setup

One command per editor. Run it in your project directory.

### Cursor

```bash
cloudeide install cursor
```

Writes `.cursor/mcp.json`. Add `--global` for `~/.cursor/mcp.json` instead.

### Claude Code

```bash
cloudeide install claude-code
```

Writes `.mcp.json` in the project root. This is the file Claude Code reads for project-scoped servers — **commit it** and everyone on the repository gets the tools without setting anything up.

### Codex

```bash
cloudeide install codex
```

Adds a labelled block to `.codex/config.toml`. The block is edited as text rather than parsed and rewritten, so your comments and ordering survive.

### Then

Restart the editor and run:

```bash
cloudeide doctor
```

It reports each tool: whether a config exists, whether it points at a working server, and what to do if not.

## What `install` writes, and what it does not

It records an **absolute path** to the Node interpreter and to the installed CLI. Editors do not always launch with your shell's `PATH`, and a bare `cloudeide` frequently fails there for reasons that are tedious to diagnose.

It **does not write your token into the config.** That is deliberate: `.mcp.json` is a file you commit. The server reads the credential from the CLI's own config file, which lives outside the repository with `0600` permissions.

If you need a token in the config anyway — a CI container with no home directory, say — set `CLOUDEIDE_TOKEN` in that environment instead of putting it in a committed file. See [authentication](authentication.md).

It **merges**. If the file already has other MCP servers, they stay. If it cannot parse the file, it stops and tells you rather than overwriting something it did not understand.

---

## The tools

Thirteen, all prefixed `cloudeide_`.

### Reading

| Tool | What it does |
|---|---|
| `cloudeide_whoami` | The account and the token's scopes |
| `cloudeide_list_projects` | Projects on the account |
| `cloudeide_list_deployments` | Recent deployments |
| `cloudeide_deployment_status` | One deployment's state |
| `cloudeide_deployment_logs` | A build log |
| `cloudeide_list_env_vars` | Variable **names** (never values) |
| `cloudeide_list_domains` | Custom domains and certificate state |
| `cloudeide_usage` | Quota and credits |

### Writing

| Tool | What it does |
|---|---|
| `cloudeide_deploy` | Build and publish a directory |
| `cloudeide_cancel_deployment` | Stop a running deploy |
| `cloudeide_rollback` | Republish an earlier deployment |
| `cloudeide_create_project` | Create a project |
| `cloudeide_set_env_var` | Set a build variable |

The write tools require a token with `deploy:write`. If yours is read-only, the agent gets a clear permission error naming the missing scope rather than a generic failure.

## What an agent does with them

The useful pattern is the loop, not any single call:

1. Agent changes code
2. `cloudeide_deploy`
3. `cloudeide_deployment_status` until it settles
4. If it failed — `cloudeide_deployment_logs`, read the error, fix the code, deploy again
5. If it succeeded — report the live URL

Step 4 is the point. An agent that can read its own build failure can usually fix it, without a human relaying compiler output.

## Manual configuration

`cloudeide install` is the supported path, but the config is not magic. `--print` shows exactly what would be written:

```bash
cloudeide install cursor --print
```

A Claude Code config looks like this:

```json
{
  "mcpServers": {
    "cloudeide": {
      "type": "stdio",
      "command": "/absolute/path/to/node",
      "args": ["/absolute/path/to/cloudeide/dist/cli.mjs", "mcp"]
    }
  }
}
```

Codex takes the same information as TOML:

```toml
[mcp_servers.cloudeide]
command = "/absolute/path/to/node"
args = ["/absolute/path/to/cloudeide/dist/cli.mjs", "mcp"]
```

## Troubleshooting

**The editor shows no CloudeIDE tools.** Restart it — most MCP clients read their config at startup only. Then `cloudeide doctor`.

**The server exits immediately.** It refuses to start without a credential. Run `cloudeide login`, or set `CLOUDEIDE_TOKEN` in the environment the editor launches with.

**Tools appear but every call fails.** Usually the token, not the config. `cloudeide whoami` checks it in one step.

More in [troubleshooting](troubleshooting.md).
