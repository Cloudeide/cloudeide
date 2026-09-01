# Authentication

The CLI and the MCP server authenticate with an **API token** you create in the dashboard. There is no password anywhere in this flow, and no token in this documentation — every value below is a placeholder.

## Creating a token

**Settings → API tokens → Create a token.**

Give it a name that says where it will live — `laptop`, `ci`, `agent-cursor`. When you later revoke one, the name is how you know which.

**It is shown once.** Tokens are stored as a hash, not as text, so the dashboard cannot show it to you again and neither can support. Copy it before closing the dialog. Lost one is not a problem — revoke it and make another.

## Scopes

Pick the narrowest one that does the job.

| Scope | Allows |
|---|---|
| `deploy:read` | Projects, deployments, logs, usage, domains, variable **names** |
| `deploy:write` | Start deploys, roll back, create projects, set variables |
| `deploy:admin` | Delete projects and domains |

`deploy:write` is what a normal deploy needs, and what the dashboard ticks by default.

A read-only token is genuinely useful: a status dashboard, a monitoring script, or an agent you want to be able to *look* at deployments without shipping anything. If a read-only token is used for a write operation, the error names the missing scope.

## Storing it

### On your machine

```bash
cloudeide login
```

Written to the CLI's config file with `0600` permissions — readable only by your user. The path is shown by `cloudeide config`, and follows `CLOUDEIDE_CONFIG_DIR`, then `XDG_CONFIG_HOME`, then `~/.config`.

### In CI

Use your CI system's secret store and expose it as an environment variable:

```yaml
env:
  CLOUDEIDE_TOKEN: ${{ secrets.CLOUDEIDE_TOKEN }}
```

No `login` step needed — the CLI reads `CLOUDEIDE_TOKEN` directly.

### For an editor / agent

Nothing extra. `cloudeide install` deliberately leaves the token out of the config file, and the MCP server reads it from the CLI's own config. That is what makes `.mcp.json` safe to commit.

## What not to do

**Do not commit a token.** Not in `.env`, not in `.mcp.json`, not in a CI config file that lives in the repository. Committed credentials survive in history long after they are deleted from the working tree.

**Do not paste one into a chat, an issue, or a screenshot.** If you are asking for help, `cloudeide whoami --json` shows the account and scopes without revealing the token.

**Prefer piping over `--token` on the command line.** A command-line argument is visible to every process on the machine through `ps` and lands in shell history:

```bash
echo "$CLOUDEIDE_TOKEN" | cloudeide login    # better
cloudeide login --token "$CLOUDEIDE_TOKEN"   # works, but visible
```

`--token` exists because it is genuinely the right tool when the value came from a secret store in a script. The CLI prints a one-line reminder when you use it.

## Rotating

1. Create the new token
2. Update wherever it is used — `cloudeide login` again, or change the CI secret
3. Revoke the old one in the dashboard

Revocation takes effect immediately.

## If a token leaks

Revoke it first, then work out the blast radius. **Settings → API tokens → Revoke.** The token stops working at once; no deploy or restart is needed.

Then rotate anything it could reach, and check `cloudeide deployments` for activity you do not recognise.

If you believe the leak points at a vulnerability in CloudeIDE rather than a mistake on your side, please report it privately — see [SECURITY.md](../SECURITY.md).

## Checking what you have

```bash
cloudeide whoami          # account and scopes
cloudeide config          # which token is in use, and where it came from
```

`cloudeide config` is the one to reach for when a command is authenticating as something you did not expect — it shows whether the value came from a flag, the environment, or the config file.
