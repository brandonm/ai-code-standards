<!-- tier: global -->
<!-- requires: none -->

## Boundaries — Do Not Do List

<!-- WHY: Some actions are catastrophic and irreversible. Encoding them as hard rules removes the temptation to "just this once" them. -->

### Destructive Operations — Never Without Explicit Approval

- `git push --force` to shared branches (main, master, develop, release/*)
- `DROP TABLE`, `DROP DATABASE`, `TRUNCATE` in production
- `rm -rf` on directories you didn't create in this session
- `git reset --hard` on branches with unpushed commits
- Deleting CI/CD pipeline configs
- Removing authentication or authorization checks

### Security Boundaries — Never

- Commit secrets, tokens, keys, or credentials to version control
- Disable TLS/SSL verification in production code
- Bypass authentication or authorization for convenience
- Store passwords in plaintext
- Execute untrusted input as code (dynamic code evaluation, shell injection, etc.)
- Disable security linters or SAST tools to make builds pass

### Process Boundaries — Never Without Asking

- Modify CI/CD pipelines or deployment configs
- Add new external dependencies (libraries, services, APIs)
- Change database schemas in production
- Modify shared infrastructure (DNS, load balancers, IAM)
- Push directly to main/master — use branches and PRs

### Scope Boundaries — Never

- Refactor code unrelated to the current task
- Add features not requested
- "Improve" working code that isn't part of the current change
- Change formatting/style in files you didn't otherwise modify

### Dependency Versions — Never Assume

- Never hardcode a dependency version from memory — always look up the latest stable version (official docs, MCP tools, package registry)
- Never copy version numbers from templates or examples without verifying they are current
- Pin versions explicitly once verified — no unpinned ranges in production
- Only pull dependencies from official registries (Maven Central, npm, PyPI, crates.io, Go module proxy)
- Verify package name spelling — check for typo-squatting (e.g., `lodash` vs `1odash`)
- Prefer well-maintained packages — check download counts, last commit date, known maintainers
- Never add dependencies from personal forks, unverified GitHub repos, or self-hosted registries without explicit approval
