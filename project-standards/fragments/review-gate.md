<!-- tier: global -->
<!-- requires: none -->

## Code Review Gate

<!-- WHY: Self-review catches a class of mistakes that tests don't: design drift, readability decay, unnecessary complexity. A checklist prevents the "looks good to me" rubber stamp. -->

Before marking any change as complete, follow this sequence:

1. **Self-review** — checklist below
2. **AI code review** — automated review tool
3. **Security scan** — secrets, SAST, SCA (see Security Scanning Gate)
4. **Peer review** — (team projects only) per your PR approval rules

All four steps must pass. No step is skippable except peer review for solo projects.

### Self-Review

Score each category:

| Category | What to check |
|----------|---------------|
| **Correctness** | Does the code do what it claims? Edge cases handled? Off-by-one errors? Null/nil/empty cases? |
| **Design** | Does it fit the existing architecture? Is the abstraction level right? Would a new team member understand the structure? |
| **Readability** | Clear naming? No clever tricks? Could someone understand this without the PR description? |
| **Error handling** | Are errors propagated, not swallowed? Are error messages actionable? Are failure modes tested? |
| **Performance** | Any O(n^2) hiding in a loop? Unbounded queries? Missing pagination? Unnecessary allocations in hot paths? |
| **Test quality** | Tests cover the happy path AND failure modes? Tests are readable? No testing implementation details? |
| **Diff hygiene** | No unrelated changes? No formatting-only diffs? No commented-out code? No TODOs without linked issues? |

### Rating

For each category, assign:
- **Pass** — no issues found
- **Note** — minor concern, acceptable to ship with a comment
- **Blocker** — must fix before merging

**If any category is rated Blocker, the change is not done.**

### AI Code Review

After self-review passes, run an AI code review before marking complete. This is **not optional** — self-review catches your own blind spots, AI review catches the rest.

- Use available AI review tools (e.g., CodeRabbit, Claude Code review skills, or equivalent)
- Address all critical/high findings before merging
- Low-severity findings can be tracked as follow-up issues if justified
- For team projects, AI review supplements but does not replace peer review — follow your PR approval rules

### When to Skip

Self-review can be skimmed (not skipped) for:
- Single-line typo fixes
- Dependency version bumps with passing CI
- Generated code (but review the generator config)

AI review should still run on all PRs regardless of size.
