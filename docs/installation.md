# Installation

## Requirements

**Node.js 20 or newer.** Check with `node --version`.

Nothing else. The CLI ships as a single bundled file with one runtime dependency, so installing it does not pull a dependency tree into your machine.

## Install

```bash
npm install -g cloudeide
```

Any package manager works — the package is on the public npm registry:

```bash
pnpm add -g cloudeide
yarn global add cloudeide
bun add -g cloudeide
```

Verify:

```bash
cloudeide --version
```

## Without installing globally

To try it once, or to pin a version in a script:

```bash
npx cloudeide@latest --version
npx cloudeide@0.1.1 deploy .
```

Slower on each run, since the package is fetched, but it leaves nothing behind.

## In CI

Install and authenticate from a secret — no interactive login:

```yaml
- run: npm install -g cloudeide
- run: cloudeide deploy . --env production --json
  env:
    CLOUDEIDE_TOKEN: ${{ secrets.CLOUDEIDE_TOKEN }}
```

Pin the version (`cloudeide@0.1.1`) if you want CI to be unaffected by new releases. See [examples](examples.md).

## Upgrading

```bash
npm install -g cloudeide@latest
```

Until `1.0.0`, treat a changed flag, exit code or JSON shape as a minor version bump — scripts and agents branch on all three, so they count as interface rather than detail.

## Uninstalling

```bash
npm uninstall -g cloudeide
```

That removes the binary but leaves your credential behind. To remove that too:

```bash
cloudeide logout           # before uninstalling
```

Or delete the config directory afterwards — `cloudeide config` prints its path. Note that `logout` only forgets the token locally; revoke it in the dashboard if it should stop working everywhere.

---

## The command is not found

Almost always the same cause: your package manager's global `bin` directory is not on `PATH`.

Find where it put things:

```bash
npm bin -g          # npm
pnpm bin -g         # pnpm
```

Then add that directory to `PATH` in your shell profile:

```bash
export PATH="$(npm bin -g):$PATH"
```

Open a new terminal afterwards — an existing shell will not pick up the change.

**pnpm specifically** needs `PNPM_HOME` set and on `PATH` before a global install has anywhere to go. `pnpm setup` does this for you.

## Permission errors on install

If `npm install -g` fails with `EACCES`, do not reach for `sudo` — it leaves root-owned files in your npm directory that cause stranger failures later. Either use a version manager (`nvm`, `fnm`, `volta`), which puts everything in your home directory, or point npm at a writable prefix:

```bash
npm config set prefix ~/.npm-global
export PATH="$HOME/.npm-global/bin:$PATH"
```

## An old version keeps running

Two copies on `PATH`. Find out which one wins:

```bash
which -a cloudeide
```

Remove the one you do not want, or fix the ordering in `PATH`.
