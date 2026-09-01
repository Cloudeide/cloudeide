# Configuration

Three sources, in this order. The first one that has a value wins.

1. **Command-line flags** — `--api-url`, `--token`, `--project`
2. **Environment variables**
3. **The config file**

When something is talking to the wrong place, `cloudeide config` shows each setting *and where it came from*. That is usually a faster answer than reading this page.

```bash
cloudeide config
cloudeide config --json
```

## Environment variables

| Variable | Purpose |
|---|---|
| `CLOUDEIDE_TOKEN` | API token. Skips the config file entirely — the usual choice for CI and containers |
| `CLOUDEIDE_API_URL` | Base URL of the API. Defaults to `https://cloudeide.com` |
| `CLOUDEIDE_PROJECT` | Default project id or slug, so you can omit `--project` |
| `CLOUDEIDE_CONFIG_DIR` | Where the config file lives. Overrides the default location |
| `CLOUDEIDE_VERBOSE` | Set to any value for extra diagnostic output on stderr |

Standard variables are honoured too: `NO_COLOR` disables colour, `FORCE_COLOR` forces it on when output is not a terminal.

## The config file

Written by `cloudeide login`, at `config.json` inside the config directory, with `0600` permissions.

The directory is resolved in this order:

1. `CLOUDEIDE_CONFIG_DIR`, if set
2. `$XDG_CONFIG_HOME/cloudeide`, if `XDG_CONFIG_HOME` is set
3. `~/.config/cloudeide`

That middle step catches people out. Some environments — CI images, cloud IDEs, container base images — set `XDG_CONFIG_HOME`, and the config then lands somewhere other than `~/.config`. `cloudeide config` prints the real path; do not assume it.

Contents:

```json
{
  "apiUrl": "https://cloudeide.com",
  "token": "<your token>",
  "defaultProjectId": null
}
```

You can edit it by hand, but `cloudeide login` is safer — it verifies the token against the server before writing, so an unusable credential never gets stored.

### Isolating a run

Point `CLOUDEIDE_CONFIG_DIR` somewhere temporary to leave your real config untouched:

```bash
CLOUDEIDE_CONFIG_DIR=$(mktemp -d) cloudeide login
```

Useful for testing, for scripts that should not depend on ambient state, and for working against two accounts at once.

## Per-project settings

Build settings — framework, build command, output directory, install command, Node version, root directory — belong to the **project**, not to your machine, and are managed in the dashboard. That is deliberate: a deploy from CI and a deploy from your laptop should build identically, which cannot be true if the settings live in someone's home directory.

Environment variables for builds are also per-project:

```bash
cloudeide env list
cloudeide env set
```

`env list` returns names only. Values do not come back out of the API once set.

## Choosing a project

Most accounts have one and never think about it. With several:

```bash
cloudeide deploy . --project my-site        # per command
export CLOUDEIDE_PROJECT=my-site            # per shell
```

`cloudeide projects list` shows ids and slugs.

## Talking to a different server

```bash
cloudeide --api-url https://staging.example.com whoami
```

`cloudeide login` checks the server before it checks the token, so a wrong URL is reported as a wrong URL — not as an authentication failure three commands later.
