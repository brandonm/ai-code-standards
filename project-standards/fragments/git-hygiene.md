<!-- tier: global -->
<!-- requires: none -->

## Git & Commit Hygiene

<!-- WHY: Git history is documentation. Sloppy commits make debugging, reverting, and onboarding harder for everyone including future-you. -->

### Commit Messages

Use **Conventional Commits** format:

```
<type>(<scope>): <short summary>

<optional body — explain WHY, not WHAT>

<optional footer — references, breaking changes>
```

Types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `perf`, `ci`, `build`

**Rules:**
- Subject line under 72 characters
- Imperative mood ("add feature" not "added feature")
- Body explains motivation, not mechanics (the diff shows the what)
- Reference issue/ticket numbers in the footer
- No WIP commits on shared branches — squash or amend before pushing

### Atomic Commits

Each commit should be:
- **One logical change** — not "fix bug and also refactor and update deps"
- **Buildable** — the project compiles/passes linting at every commit
- **Revertable** — reverting this commit undoes exactly one thing

### Branch Strategy

- Never push directly to main/master
- Branch names: `<type>/<short-description>` (e.g., `feat/user-auth`, `fix/null-pointer-cart`)
- Delete branches after merge
- Keep branches short-lived — merge within days, not weeks

### Pull Requests

- PR title follows Conventional Commits format
- PR description includes: what changed, why, how to test
- One concern per PR — don't bundle unrelated changes
- Link to the issue/ticket being addressed
- All CI checks must pass before merge
