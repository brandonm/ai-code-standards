<!-- tier: project -->
<!-- requires: boundaries -->

## Solo Developer — Adjusted Guardrails

<!-- WHY: Solo devs don't need the same process overhead as teams, but still need protection against irreversible mistakes. These adjustments relax the process rules while keeping the safety rails. -->

### Relaxed Rules

- **Direct push to main is acceptable** for small, tested changes — but prefer branches for anything non-trivial
- **PR descriptions can be brief** — you're not explaining to a reviewer, but still write enough to remind future-you
- **Approval gates are self-approval** — pause, re-read the plan, then proceed. Don't skip the pause.

### Still Non-Negotiable

Everything in the base `boundaries.md` still applies. Specifically:

- Tests still ship with code
- Coverage gate still enforced
- Security scanning still runs
- No force-push without thinking twice (you can't blame a teammate for the rebase that ate your commits)
- Secrets still never go in version control
