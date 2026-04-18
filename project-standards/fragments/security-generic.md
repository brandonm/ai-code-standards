<!-- tier: global -->
<!-- requires: none -->

## Security Scanning Gate

<!-- WHY: Security vulnerabilities found in production cost 10-100x more to fix than ones caught in CI. Automated scanning removes the "I forgot to check" failure mode. -->

### Secrets Detection

Run a secrets scanner before every commit. Recommended tools:
- **gitleaks** — fast, low false-positive rate
- **trufflehog** — deeper history scanning

Block commits containing:
- API keys, tokens, passwords
- Private keys (RSA, SSH, PGP)
- Connection strings with credentials
- `.env` files with real values

**Pre-commit hook or CI check — not optional.**

### Static Analysis (SAST)

Run language-appropriate SAST on every PR:

| Language | Tool |
|----------|------|
| Go | `gosec`, `staticcheck` |
| Java | `spotbugs`, `semgrep` |
| Python | `bandit`, `semgrep` |
| JavaScript/TypeScript | `semgrep`, `eslint-plugin-security` |
| Rust | `cargo clippy` (includes security lints), `cargo audit` |
| Multi-language | `semgrep` (covers most languages with community rules) |

Fix findings before merging. If a finding is a false positive, suppress it with an inline annotation and a comment explaining why.

### Dependency Scanning (SCA)

Check dependencies for known CVEs:

| Ecosystem | Tool |
|-----------|------|
| Go | `govulncheck` |
| Java/Gradle | `dependencyCheckAnalyze` (OWASP plugin) |
| Python | `pip-audit`, `safety` |
| Node | `npm audit`, `yarn audit` |
| Rust | `cargo audit` |

**Block merges on critical/high CVEs.** Medium/low can be tracked as issues.

### Input Validation Rules

- Validate all external input at system boundaries (HTTP requests, CLI args, file uploads, queue messages)
- Use allowlists over denylists
- Parameterize all database queries — no string concatenation
- Sanitize output for the target context (HTML, SQL, shell, logs)

### Authentication & Authorization

- Every endpoint must declare its auth requirement (public, authenticated, role-based)
- Default to **deny** — endpoints without explicit auth config must reject requests
- Never roll your own crypto or auth protocol
- Session tokens: httpOnly, secure, sameSite=strict (for web)
