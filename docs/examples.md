# Examples

Every value below is a placeholder. Never commit a real token.

## Scripting against `--json`

`--json` prints one document on stdout and nothing else, so it can be piped straight into a parser.

```bash
URL=$(cloudeide deploy . --env production --json | jq -r '.liveUrl')
echo "Deployed to $URL"
```

Errors are JSON too, with a stable `code`:

```bash
if ! OUT=$(cloudeide deploy . --json 2>/dev/null); then
  CODE=$(printf '%s' "$OUT" | jq -r '.error.code')
  case "$CODE" in
    out_of_credits) echo "Top up in the dashboard"; exit 1 ;;
    unauthenticated) echo "Token expired or revoked"; exit 1 ;;
    *) echo "Deploy failed: $CODE"; exit 1 ;;
  esac
fi
```

Branch on the **exit code** when you do not need details:

```bash
cloudeide deploy . --quiet
case $? in
  0) echo "shipped" ;;
  3) echo "not signed in" ;;
  7) echo "out of credits" ;;
  9) echo "server unreachable — try later" ;;
esac
```

## GitHub Actions

```yaml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install CloudeIDE CLI
        run: npm install -g cloudeide@0.1.1

      - name: Deploy
        env:
          CLOUDEIDE_TOKEN: ${{ secrets.CLOUDEIDE_TOKEN }}
        run: cloudeide deploy . --env production --json
```

Three things worth copying:

- **The version is pinned.** CI that silently follows `latest` will one day fail for a reason unrelated to your commit.
- **No `login` step.** `CLOUDEIDE_TOKEN` is read directly, so the credential never touches disk.
- **The token is a repository secret**, not a value in the workflow file.

## Preview deploy per pull request

```yaml
- name: Deploy preview
  id: preview
  env:
    CLOUDEIDE_TOKEN: ${{ secrets.CLOUDEIDE_TOKEN }}
  run: |
    URL=$(cloudeide deploy . --env preview --json | jq -r '.liveUrl')
    echo "url=$URL" >> "$GITHUB_OUTPUT"

- name: Comment the URL
  uses: actions/github-script@v7
  with:
    script: |
      github.rest.issues.createComment({
        issue_number: context.issue.number,
        owner: context.repo.owner,
        repo: context.repo.repo,
        body: `Preview: ${{ steps.preview.outputs.url }}`
      })
```

A read-only token is not enough here — previews are deploys, so this needs `deploy:write`.

## Long builds without holding the connection

```bash
ID=$(cloudeide deploy . --no-wait --json | jq -r '.deploymentId')

while true; do
  STATUS=$(cloudeide status "$ID" --json | jq -r '.status')
  case "$STATUS" in
    success) echo "done"; break ;;
    failed|canceled) cloudeide logs "$ID"; exit 1 ;;
    *) sleep 10 ;;
  esac
done
```

## Checking before you upload

```bash
cloudeide deploy . --dry-run
```

Reports what *would* be sent without deploying. Worth running once on a new project — the file count and total size tell you about an accidentally-included directory before you upload it rather than after.

## Rolling back

```bash
cloudeide deployments                  # find the id of the last good one
cloudeide rollback dep_xxxxxxxx
```

The build is not re-run. The previous output is promoted back to the live path, so it is quick and cannot fail in some new way.

## Environment variables for builds

```bash
cloudeide env set
cloudeide env list        # names only — values never come back out
```

These are build-time variables for your project, and belong to the project rather than your machine, so CI and your laptop build identically.

## Two accounts on one machine

```bash
CLOUDEIDE_CONFIG_DIR=~/.config/cloudeide-work cloudeide login
CLOUDEIDE_CONFIG_DIR=~/.config/cloudeide-work cloudeide deploy .
```

Or as a shell function:

```bash
work() { CLOUDEIDE_CONFIG_DIR=~/.config/cloudeide-work cloudeide "$@"; }
work whoami
```

## Asking an agent to do it

With the MCP server configured ([setup](mcp.md)), the useful instructions are outcome-shaped rather than command-shaped:

> Deploy this to preview and give me the URL.

> The last deploy failed. Read the build log, work out why, fix it, and deploy again.

> What did we ship today, and did any of it fail?

The second one is the reason MCP is worth configuring: the agent reads the failure itself instead of asking you to paste it.
