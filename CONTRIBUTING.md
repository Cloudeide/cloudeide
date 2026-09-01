# Contributing

Thanks for taking the time. Issues and pull requests are both welcome.

## Reporting a bug

A good report is one someone else can reproduce. Please include:

**What you ran and what happened.** The exact command, and the output — not a summary of it.

**Diagnostics.** These three contain no secrets:

```bash
cloudeide --version
cloudeide config --json
cloudeide doctor --json
```

`config --json` reports *where* each setting came from, and reports the token only as `authenticated: true` — never the value.

**Environment.** Operating system, Node version (`node --version`), and how you installed the CLI.

**What you expected instead.** Sometimes the behaviour is intended and the documentation is what is wrong — that is still worth fixing, and worth knowing.

### Never include

- API tokens, even partially or "redacted" — a prefix still identifies the token
- Passwords, private keys, or connection strings
- Screenshots with credentials visible in a terminal

If a credential has already been posted somewhere public, revoke it first: **Settings → API tokens → Revoke**.

### Security issues do not go here

Do not open a public issue for a vulnerability. See [SECURITY.md](SECURITY.md).

## Suggesting a feature

Describe the problem before the solution. "I cannot tell whether a deploy is still running from a script" is more useful than "add a `--watch` flag", because it leaves room for a better answer than the one you had in mind.

Worth mentioning: what you do today instead, and whether a workaround exists.

## Pull requests

**Small and focused beats large and comprehensive.** A PR that does one thing gets reviewed quickly; one that does four gets stuck on whichever part is most contentious.

**Say why in the description.** What the code does is visible in the diff. Why it needed doing is not, and it is what review actually turns on.

**Match the surrounding code.** Naming, structure, and comment density. Comments here explain *why* something is the way it is — especially where the obvious approach was rejected for a reason that is not obvious.

**Include a test when the change is testable**, and make sure it fails without your fix. A test that passes either way documents nothing.

**Do not commit generated output or lockfile churn** unrelated to your change.

### Before opening

```bash
npm run typecheck
npm test
```

### Interface changes

The CLI's flags, exit codes and `--json` shapes are an interface — scripts and AI agents branch on all three. Changing one is a breaking change even when the code looks like a detail. If your PR changes any of them, say so explicitly in the description.

## Documentation

Documentation fixes are real contributions and get reviewed like code. If something in `docs/` is wrong, unclear, or describes behaviour that no longer exists, a PR fixing it is very welcome — and so is an issue, if you would rather just report it.

When documenting a command, check the behaviour against the binary rather than against another document. Docs drift; the binary does not.

## Review

Expect a first response within a few days. Review usually means questions rather than a verdict — the goal is understanding the change, not gatekeeping it.

If a PR is not going to be merged, you will be told why, rather than left waiting.

## Code of conduct

Be straightforward and civil. Critique the work, not the person. Assume the other party is trying to help, because they almost always are.

Behaviour that makes the project worse to participate in — harassment, personal attacks, deliberate bad faith — is not tolerated, and maintainers will remove it.
