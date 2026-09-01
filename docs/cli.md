# CLI reference

```
cloudeide <command> [options]
```

Everything below is also available as `cloudeide <command> --help`.

## Global options

These work on every command.

| Flag | Effect |
|---|---|
| `--json` | Print the result as one JSON document on stdout, and nothing else |
| `--quiet`, `-q` | Suppress progress output; errors still print |
| `--project`, `-p` | Which project, by id or slug |
| `--api-url` | Talk to a different server |
| `--token` | Use this token instead of the stored one |
| `--timeout <s>` | Per-request timeout in seconds (default 30) |
| `--no-color` | Never colour the output |
| `--help`, `-h` | Show help for the command |

### About `--json`

`--json` guarantees that stdout is a single parseable document. Progress, warnings and prompts go to stderr or are suppressed entirely. Errors are *also* JSON, on stdout, with a machine-readable `code` — so a script never has to parse an error message.

```json
{
  "ok": false,
  "error": {
    "code": "unauthenticated",
    "message": "Not signed in.",
    "retryable": false
  }
}
```

### About `--token`

Convenient in CI where the value comes from a secret store. Be aware it is visible to other processes via `ps` and lands in shell history — piping to `cloudeide login`, or setting `CLOUDEIDE_TOKEN`, avoids both. The CLI prints a one-line warning when you use it.

## Exit codes

Specific by design, so scripts can branch on the failure rather than grep the message.

| Code | Meaning |
|---|---|
| `0` | Success |
| `1` | Failed |
| `2` | Wrong usage |
| `3` | Not authenticated |
| `4` | Token lacks permission |
| `5` | Not found |
| `6` | Over a limit |
| `7` | Out of credits |
| `8` | Conflicting state |
| `9` | Server unreachable |

---

## Identity

### `cloudeide login`

Store an API token for this machine.

```bash
cloudeide login                      # prompts, without echoing
echo "$TOKEN" | cloudeide login      # from a pipe
cloudeide login --token "$TOKEN"     # from a flag (see the caveat above)
```

The token is verified against the server before it is written. The config file is created with `0600` permissions — readable only by you.

### `cloudeide logout`

Forget the stored token. The token itself stays valid — revoke it in the dashboard if it was exposed.

### `cloudeide whoami`

Show the account this token belongs to and what it may do.

```bash
cloudeide whoami --json
```

### `cloudeide config`

Show the settings in use **and where each came from** — flag, environment variable, config file or default. The first thing to run when a command is talking to the wrong place.

---

## Deploying

### `cloudeide deploy [dir]`

```
cloudeide deploy [dir] [--env production] [--project slug] [--no-wait] [--dry-run]
```

Build a directory and publish it. Defaults to the current directory.

| Flag | Effect |
|---|---|
| `--env` | Which environment (e.g. `production`, `preview`) |
| `--project` | Which project, by id or slug |
| `--no-wait` | Start the deploy and return immediately, printing the id |
| `--dry-run` | Collect and report what *would* be sent, without deploying |

```bash
cloudeide deploy .
cloudeide deploy ./site --env production
cloudeide deploy . --no-wait --json     # for CI that polls separately
cloudeide deploy . --dry-run            # see what would be uploaded
```

`--dry-run` is worth running once on a new project. It shows the file count and total size, so you find out about an accidentally-included directory before uploading it rather than after.

### `cloudeide status [id]`

Show a deployment, or the most recent one if no id is given.

### `cloudeide logs [id]`

Print a deployment's build log.

### `cloudeide cancel <id>`

Stop a running deployment.

### `cloudeide rollback <id>`

Republish an earlier deployment. The build is not re-run — the previous output is promoted back to the live path, so it is fast and cannot fail differently than it did the first time.

### `cloudeide deployments`

List recent deployments.

---

## Projects

### `cloudeide projects list`

### `cloudeide projects create`

Most accounts need only one project. Create more when you want separate environments, domains and variables that shouldn't see each other.

---

## Environment variables

### `cloudeide env list`

Lists variable **names**, never values. Secrets do not come back out.

### `cloudeide env set`

Set a variable for a project's builds.

---

## Domains

### `cloudeide domains list`

Custom domains attached to a project, with their certificate status.

---

## Account

### `cloudeide usage`

Quota and credit use.

### `cloudeide server-status`

What the server supports — useful when a command exists in your CLI but the server hasn't got it yet.

---

## AI tooling

### `cloudeide install <editor>`

```
cloudeide install <cursor|claude-code|codex> [--global] [--print] [--force]
```

| Flag | Effect |
|---|---|
| `--global` | Write the user-level config instead of the project one |
| `--print` | Print the config that would be written, without writing it |
| `--force` | Overwrite an existing CloudeIDE entry |

Merges into an existing config rather than replacing it, preserving other servers and your own comments. See [MCP setup](mcp.md).

### `cloudeide doctor [editor]`

Check an AI coding tool's setup and say what is wrong. With no argument, checks all three.

Exits `0` when everything passes, `3` when no credential is present, `1` when something else is wrong.

### `cloudeide mcp`

Run the MCP server on stdio. You don't run this yourself — it is what the editor configs launch. It needs a token in the environment and exits with a message if one isn't there.
