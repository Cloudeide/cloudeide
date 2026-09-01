# Security policy

## Reporting a vulnerability

**Please do not open a public issue for a security problem.** A public report tells everyone about the weakness at the same moment it tells us, including people who would use it.

Report it privately instead:

Use [Report a vulnerability](https://github.com/cloudeide/cloudeide/security/advisories/new) on this repository's Security tab. That opens a private advisory only the maintainers can see, and lets us discuss it with you in the same place.

You will get an acknowledgement within **3 business days**. If you have not heard anything by then, please chase it — do not assume the report was received and ignored.

## What to include

The more of this you can give, the faster it gets fixed:

- What the issue is, and what an attacker could achieve with it
- Steps to reproduce, or a proof of concept
- Affected versions — `cloudeide --version`, or the date if it concerns the hosted service
- Anything that makes it easier or harder to exploit

If reproducing it required an account, say which account, so we can trace the activity in our own logs.

**Do not include real credentials in the report.** If a token was involved, revoke it first and describe it rather than pasting it.

## What happens next

1. **Acknowledgement** within 3 business days
2. **Assessment** — we confirm it and work out the severity and reach
3. **A fix**, prioritised by severity
4. **Release**, with a note in the advisory
5. **Credit** to you in the advisory, unless you would rather stay anonymous

We will keep you updated as it moves, and tell you before any public disclosure.

## Scope

**In scope**

- The `cloudeide` npm package — CLI and MCP server
- The hosted API at `cloudeide.com`
- The CloudeIDE web application
- Anything in this repository

**Out of scope**

- Vulnerabilities in third-party dependencies that we do not control — please report those upstream, though we do want to hear if we are shipping a known-vulnerable version
- Findings from automated scanners with no demonstrated impact
- Social engineering of CloudeIDE staff or users
- Denial of service through sheer volume
- Missing hardening headers or best practices with no exploitable consequence

## Safe harbour

If you make a good-faith effort to follow this policy, we will not pursue legal action over your research. Good faith means:

- Only touching accounts and data that belong to you
- Stopping as soon as you have confirmed a vulnerability, rather than exploring how far it goes
- Not degrading the service for other people
- Not accessing, changing or keeping anyone else's data
- Giving us reasonable time to fix it before going public

If you are unsure whether something is in bounds, open a private advisory and ask — an advisory costs nothing and can be closed.

## If your own token leaks

That is not a vulnerability report — it is an account action, and you can fix it yourself immediately:

1. **Settings → API tokens → Revoke.** It stops working at once.
2. Create a replacement and update wherever it was used.
3. Check `cloudeide deployments` for activity you do not recognise.

Report it to us as well if you think the leak was caused by CloudeIDE rather than by a mistake on your side — for example if a token appeared somewhere our tooling put it.

## Supported versions

Fixes go into the latest release. Until `1.0.0`, please upgrade to the current version before reporting, in case the problem is already fixed.

| Version | Supported |
|---|---|
| Latest `0.1.x` | ✅ |
| Older | Please upgrade first |
