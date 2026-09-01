# FAQ

## General

**What is CloudeIDE?**

A cloud development environment that runs in a browser tab — editor, AI agent, sandboxed execution and deployment, with nothing to install. This repository is the CLI and MCP server for driving it from outside the browser.

**Do I need the CLI to use CloudeIDE?**

No. The browser IDE needs nothing installed. The CLI is for people who want the same account from their own terminal, or who want an AI coding agent to build and ship without a human relaying output.

**Is this open source?**

The CLI and MCP server in this repository are MIT-licensed. The hosted platform is a commercial service.

**What does it cost?**

Deployments consume credits, shown per deploy before it runs and readable with `cloudeide usage`. Current pricing is on [cloudeide.com](https://cloudeide.com).

---

## Installing and using

**Which Node version?**

20 or newer.

**Do I have to use npm?**

No — pnpm, yarn and bun all work. The package is on the public npm registry.

**Can I use it without installing?**

`npx cloudeide@latest <command>`. Slower per run, but leaves nothing behind.

**Does it work on Windows?**

Yes, on Node 20+. The editor configs are written to the same paths each tool uses on Windows.

**Can I run it in CI?**

Yes, and that is the main non-interactive use. Set `CLOUDEIDE_TOKEN` from your CI secret store — no `login` step, and the credential never touches disk. See [examples](examples.md#github-actions).

---

## Tokens

**Where do I get a token?**

Dashboard → **Settings → API tokens**.

**I lost my token.**

It cannot be recovered — tokens are stored as hashes, so nobody can read one back, including us. Revoke it and create another.

**Which scope do I need?**

`deploy:write` for deploying. `deploy:read` is enough for status, logs and usage. `deploy:admin` only for deleting projects and domains. Pick the narrowest one that works.

**Can I change a token's scopes later?**

No — scopes are fixed at creation. Create a new token and revoke the old one.

**Does the token expire?**

It stays valid until you revoke it, unless you set an expiry when creating it.

**Is it safe to commit `.mcp.json`?**

Yes, and that is the point of the design — `cloudeide install` never writes the token into it. The MCP server reads the credential from the CLI's own config, outside the repository.

---

## Deploying

**What can I deploy?**

Static sites and front-end builds. A directory with a `package.json` is built with the detected framework's command; a plain `index.html` with no `package.json` is served as-is.

**How do I set the build command?**

In the dashboard, on the project. Build settings live with the project rather than on your machine, so CI and your laptop build identically.

**Where does the URL come from?**

Each deploy gets a public URL, printed when it finishes and available later from `cloudeide status`. Custom domains are attached in the dashboard and listed by `cloudeide domains list`.

**Can I deploy to more than one environment?**

Yes — `--env production`, `--env preview`, and so on. Each keeps its own deployment history.

**How do I undo a bad deploy?**

```bash
cloudeide deployments
cloudeide rollback <id>
```

The build is not re-run; the earlier output is promoted back, so it is fast and cannot fail differently than it did originally.

**Are my environment variables visible to anyone?**

`cloudeide env list` returns names only. Values do not come back out of the API once set.

---

## MCP and agents

**What is MCP?**

The [Model Context Protocol](https://modelcontextprotocol.io/) — a standard way for AI tools to call external tools. It is what lets Cursor, Claude Code or Codex operate CloudeIDE directly.

**Which editors are supported?**

Cursor, Claude Code and Codex, each with `cloudeide install <editor>`. Any MCP-capable client can use the server; those three get their config written automatically.

**What can the agent actually do?**

Thirteen tools — deploy, cancel, roll back, create projects, set variables, and read status, logs, deployments, domains, usage and identity. Full list in [MCP setup](mcp.md#the-tools).

**Can the agent do something destructive?**

It is bounded by the token's scopes. Give it a `deploy:read` token and it can look but not ship. `deploy:admin` — the only scope that can delete — is never required for normal work, so do not give an agent one unless you mean to.

**Do I have to configure it per project?**

`cloudeide install <editor>` writes a project config by default; `--global` writes the user-level one instead. For Claude Code, the project file is usually what you want, because committing it gives the whole team the tools.

**Is CloudeIDE in the MCP Registry?**

Yes, as `com.cloudeide/cloudeide`, with the namespace verified by DNS on `cloudeide.com`.

---

## Problems

**Why does `cloudeide login` say I am not signed in afterwards?**

The CLI is probably reading a different config directory than you think. `XDG_CONFIG_HOME` takes precedence over `~/.config` and is set in more environments than people expect. `cloudeide config` prints the real path.

**Why does my editor show no tools?**

Restart it — most MCP clients read their config only at startup. Then `cloudeide doctor`.

**How do I report a bug?**

[Open an issue](https://github.com/cloudeide/cloudeide/issues) with the output of `cloudeide --version`, `cloudeide config --json` and `cloudeide doctor --json`. None of those contain your token.

**How do I report a security problem?**

Privately, not in an issue — see [SECURITY.md](../SECURITY.md).
