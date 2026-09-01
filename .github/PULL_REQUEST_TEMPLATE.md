## What this changes

<!-- What the change does, in a sentence or two. -->

## Why

<!-- The diff shows what. This is for why — the reason the change was needed,
     and why this approach rather than an obvious alternative. This is what
     review actually turns on. -->

## How it was verified

<!-- Commands run, tests added, what you checked by hand. If a test was added,
     confirm it fails without the fix — a test that passes either way
     documents nothing. -->

## Interface changes

<!-- The CLI's flags, exit codes and --json shapes are an interface: scripts
     and AI agents branch on all three. Tick anything this PR changes. -->

- [ ] Adds or changes a command or flag
- [ ] Changes an exit code
- [ ] Changes a `--json` output shape
- [ ] Changes an MCP tool name, arguments or result
- [ ] None of the above

## Checklist

- [ ] `npm run typecheck` passes
- [ ] `npm test` passes
- [ ] Documentation updated, if behaviour changed
- [ ] No credentials, tokens or private data in the diff
