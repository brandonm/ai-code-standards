<!-- tier: project -->
<!-- requires: boundaries -->

## Team Project — Additional Guardrails

<!-- WHY: Shared repos multiply the blast radius of mistakes. One bad force-push affects everyone. These rules protect the team from each other's worst moments. -->

### Branch Protection

- **main/master is protected** — no direct pushes, no force-pushes
- All changes go through pull requests with at least one approval
- CI must pass before merge is allowed
- Stale approvals are dismissed on new pushes

### Code Ownership

- Define CODEOWNERS for critical paths (auth, payments, infra, CI)
- Changes to owned paths require approval from the owning team/person
- Don't bypass CODEOWNERS — if the owner is unavailable, escalate, don't merge around them

### Communication

- Breaking changes require a heads-up before merging (Slack, standup, whatever the team uses)
- If you're about to refactor a file someone else is actively working on — talk to them first
- Dependency upgrades that change behavior should be announced, not surprised

### Shared Environment Rules

- Don't modify shared dev/staging environments without announcing it
- Lock shared test databases before running destructive test suites
- Tag deployments so anyone can see what's running where
