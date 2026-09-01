# Troubleshooting

Organised by what you see. Two commands answer most questions:

```bash
cloudeide config     # what settings are in use, and where each came from
cloudeide doctor     # editor setup, checked and explained
```

---

## Installation

### `cloudeide: command not found`

The global `bin` directory is not on `PATH`. See [installation](installation.md#the-command-is-not-found).

### An old version runs after upgrading

Two copies on `PATH`:

```bash
which -a cloudeide
```

### `EACCES` when installing

Do not use `sudo` — it leaves root-owned files that cause worse problems later. See [installation](installation.md#permission-errors-on-install).

---

## Authentication

### `Not signed in.` (exit 3)

No credential was found. Either run `cloudeide login`, or set `CLOUDEIDE_TOKEN`.

If you *have* logged in and still see this, the CLI is probably reading a different config directory than you expect — `XDG_CONFIG_HOME` is set in more environments than people realise:

```bash
cloudeide config
```

That prints the real path. Do not assume `~/.config`.

### `This token does not have permission for that.` (exit 4)

The token is valid but lacks the scope. Deploying needs `deploy:write`; a `deploy:read` token can look but not ship.

```bash
cloudeide whoami       # shows the token's scopes
```

Create a new token with the right scope — scopes are fixed when a token is created.

### It authenticates as the wrong account

Precedence: flags, then environment, then config file. A stale `CLOUDEIDE_TOKEN` exported in your shell profile silently wins over `cloudeide login`.

```bash
cloudeide config       # says which source won
```

### A token stopped working

It was revoked, or it expired. Create a new one and `cloudeide login` again. Revocation is immediate.

---

## Deploying

### `This server's database is missing something this feature needs.`

The server is running a build newer than its database schema. Nothing you can do from the client, and retrying will not help — that is why the message says so. Report it if you are seeing it on `cloudeide.com`.

### The deploy succeeds but the site is empty

The build produced nothing at the expected output directory. Check the project's output directory setting in the dashboard against what your build actually writes — `dist` and `build` are the usual mismatch.

```bash
cloudeide logs
```

### The build fails but works locally

Usually one of three things:

- **A missing environment variable.** Build variables belong to the project, not your machine — `cloudeide env list` shows which names exist.
- **A dependency that is only installed locally.** If it is in `devDependencies` and your build needs it, it needs to be a dependency.
- **Case-sensitivity.** macOS and Windows are usually case-insensitive; the build environment is not. `import './Button'` will not find `button.tsx`.

### Uploading more than expected

```bash
cloudeide deploy . --dry-run
```

Shows what would be sent. `node_modules`, `.git` and anything in `.gitignore` are excluded already; a large count usually means build output or assets committed somewhere unexpected.

### `Out of credits` (exit 7)

Top up in the dashboard. `cloudeide usage` shows current consumption.

### A deploy is stuck

```bash
cloudeide status
cloudeide cancel <id>
```

### It deployed but the URL 404s

Give a first deploy to a new environment a few minutes — a fresh CDN distribution takes time to propagate. `cloudeide status` reports when it has settled.

---

## MCP and editors

### The editor shows no CloudeIDE tools

1. **Restart the editor.** Most MCP clients read their config only at startup.
2. `cloudeide doctor` — confirms the config exists and is valid.
3. Check you configured the tool you are actually using. `.cursor/mcp.json` does nothing for Claude Code.

### The MCP server exits immediately

It refuses to start without a credential, by design, and says so on stderr. Run `cloudeide login`, or set `CLOUDEIDE_TOKEN` in the environment the *editor* launches with — which is often not your shell's environment.

### Tools are listed but every call fails

The config is fine; the token is not.

```bash
cloudeide whoami
```

### `install` says it cannot parse the config

There is already a file there that is not valid JSON or TOML. The CLI stops rather than overwriting something it did not understand. Fix or move the file, then run `install` again.

### It worked, then broke after reinstalling the CLI

The config records an absolute path to the installed package. Reinstalling can move it.

```bash
cloudeide install cursor --force
```

---

## Network

### `Server unreachable` (exit 9)

A proxy, VPN or firewall between you and the API. Confirm the URL first:

```bash
cloudeide config
cloudeide server-status
```

### Timeouts on large deploys

```bash
cloudeide deploy . --timeout 120
```

Or use `--no-wait` and poll — see [examples](examples.md#long-builds-without-holding-the-connection).

---

## Still stuck

Include this in your report — it contains no secrets:

```bash
cloudeide --version
cloudeide config --json
cloudeide doctor --json
```

`config --json` shows *where* settings came from, not the token itself. Then [open an issue](https://github.com/cloudeide/cloudeide/issues).

For a suspected vulnerability, do not open a public issue — see [SECURITY.md](../SECURITY.md).
