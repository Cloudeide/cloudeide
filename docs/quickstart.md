# Quick start

From nothing to a public URL. About five minutes, most of it waiting for a build.

## 1. Install

```bash
npm install -g cloudeide
```

Check it landed:

```bash
cloudeide --version
```

If the command isn't found, your package manager's global `bin` directory isn't on `PATH` — see [installation](installation.md#the-command-is-not-found).

## 2. Get a token

In the CloudeIDE dashboard: **Settings → API tokens → Create a token**.

Give it a name you'll recognise later (`laptop`, `ci`, `agent`) and leave `deploy:write` ticked — that's what deploying needs.

**The token is shown once.** It's stored as a hash, so nobody can read it back to you afterwards, including us. Copy it before you close the dialog.

## 3. Sign in

```bash
cloudeide login
```

It prompts for the token without echoing it. If you're scripting, pipe it instead:

```bash
echo "$CLOUDEIDE_TOKEN" | cloudeide login
```

`login` checks the token against the server *before* storing it, so a typo fails immediately rather than at your next command.

Confirm:

```bash
cloudeide whoami
```

## 4. Deploy

```bash
cd my-project
cloudeide deploy .
```

What happens:

1. Files are collected from the directory — ignoring `node_modules`, `.git` and anything in your `.gitignore`
2. Your build command runs in an isolated sandbox
3. The build output is checked, uploaded, and promoted to the live path
4. A public URL is printed

If the directory has a `package.json`, the framework is detected and the usual build command is used. A plain `index.html` with no `package.json` is served as-is.

Watch it again later:

```bash
cloudeide status          # the most recent deployment
cloudeide logs            # its build log
```

## 5. Give it to your agent

```bash
cloudeide install cursor
```

Restart the editor, and the agent can deploy, check status, read logs and roll back on its own. Same for `claude-code` and `codex`.

Verify:

```bash
cloudeide doctor
```

## What to read next

- [CLI reference](cli.md) — every command and flag
- [MCP setup](mcp.md) — what the agent can actually do, tool by tool
- [Examples](examples.md) — CI, preview environments, scripting

## Something went wrong

[Troubleshooting](troubleshooting.md) is organised by symptom. The short version:

| Exit code | Meaning |
|---|---|
| `3` | Not signed in — run `cloudeide login` |
| `4` | The token exists but lacks the scope for this command |
| `7` | Out of credits |
| `9` | Can't reach the server |
