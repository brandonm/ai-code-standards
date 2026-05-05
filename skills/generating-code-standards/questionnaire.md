# Project Standards Questionnaire

Ask these questions to gather inputs for CLAUDE.md generation. Auto-detect answers where possible (see SKILL.md Step 2) and present them for confirmation rather than asking from scratch.

## Questions

### 1. Project Stacks

> What stacks does this project use?

A project can have one or more stacks. Each stack is a **language + build tool + framework** combination. The framework can be "None."

Examples:
- Single stack: `Go / go build / Gin`
- Single stack, no framework: `Rust / cargo / None`
- Full-stack: `Backend: Java / Gradle / Spring Boot` + `Frontend: TypeScript / npm / React (Next.js)`
- Full-stack, no backend framework: `Backend: Go / go build / None` + `Frontend: TypeScript / npm / Vue (Nuxt)`

**Auto-detect from:** Presence of multiple manifest files indicates multiple stacks.

| Manifest | Stack |
|----------|-------|
| `build.gradle`, `pom.xml` | Java backend |
| `go.mod` | Go backend |
| `Cargo.toml` | Rust backend |
| `pyproject.toml`, `requirements.txt` | Python backend |
| `package.json` (with React/Vue/Svelte deps) | Frontend |
| `package.json` (without frontend framework deps) | Node backend |

**Presentation format:**

```
I detected the following stacks:

  Backend:  Go / go build / Gin
  Frontend: TypeScript / npm / React (Next.js)

Does this look right? If a stack has no framework, I'll mark it as "None."
```

If only one manifest is found, present as a single stack. The user can always add a second stack or say "None" for the framework.

### 3. Project Type

> What kind of project is this?

Options:
- **Backend service** — API, microservice, server
- **CLI tool** — command-line application
- **Library** — reusable package/module published for others
- **Web app** — frontend or full-stack web application
- **Data pipeline** — ETL, batch processing, data engineering
- **Embedded / systems** — low-level, resource-constrained
- **Monorepo** — multiple projects in one repo

### 4. Solo or Team

> Are you the sole contributor, or is this a team project?

- **Solo** — you're the only committer; relaxed guardrails
- **Team** — multiple contributors; stricter branch protection, review requirements

### 5. Regulated Data

> Does this project handle regulated or sensitive data?

Options:
- **None** — no special compliance requirements
- **PII** — personally identifiable information (names, emails, addresses)
- **PHI** — protected health information (HIPAA)
- **PCI** — payment card data (PCI-DSS)
- **Government** — FedRAMP, ITAR, or similar
- **Multiple** — specify which apply

If any option other than "None," the `security-regulated.md` fragment is included.

**Follow-up if "Government" is selected:**

> What agency or compliance framework applies? (e.g., FedRAMP, ITAR, CMMC, FISMA, NIST 800-171)
> Do you have a handling guide or security control baseline we should reference?

Insert the user's answer into the `Project-Specific Rules` section — government requirements are too varied to template.

### 6. CI/CD System

> What CI/CD system does this project use?

Options:
- GitHub Actions
- GitLab CI
- Woodpecker CI
- Jenkins
- CircleCI
- Bitbucket Pipelines
- None yet
- Other (specify)

**Auto-detect from:** `.github/workflows/`, `.gitlab-ci.yml`, `.woodpecker/`, `.woodpecker.yml`, `Jenkinsfile`, `.circleci/`, `bitbucket-pipelines.yml`

### 7. Testing Types

> What types of testing does this project need?

Options (select all that apply):
- **Unit** — isolated tests for individual functions/classes
- **Integration** — tests that verify components work together (DB, APIs, message queues)
- **End-to-end (E2E)** — full system tests simulating real user flows
- **Contract** — API contract verification between services (Pact, Spring Cloud Contract)
- **Performance / Load** — throughput, latency, stress testing (k6, Gatling, JMeter)
- **Property-based** — randomized input testing (QuickCheck, Hypothesis, jqwik, proptest)
- **UI / Visual** — component rendering, screenshot regression, interaction testing (Storybook, Chromatic, Playwright visual comparisons)

Default: **Unit + Integration** (most projects need at least these two).

**Auto-detect from:** test directory structure (`test/unit/`, `test/integration/`, `test/e2e/`, `tests/`), test config files (`cypress.config.*`, `playwright.config.*`, `k6/`, `gatling/`), test dependencies in manifests, UI test indicators (`.storybook/`, `chromatic.config.*`).

### 8. Coverage Threshold

> What minimum test coverage percentage do you want to enforce?

Default: **95%** (line + branch)

Let the user lower it if they have a reason, but nudge toward 90%+ as a floor.

### 9. UI Present

> Does this project have a user-facing UI?

- **Yes** — include `ui-accessibility.md` fragment
- **No** — skip accessibility rules

### 10. Existing Conventions

> Are there any existing conventions, patterns, or rules you want preserved?

Freeform answer. Examples:
- "We use the repository pattern for data access"
- "All errors must be wrapped with context before returning"
- "Never use ORM; raw SQL only"
- "Feature flags via LaunchDarkly"

These get added verbatim to a `## Project-Specific Rules` section.

### 11. Anything Else

> Any domain-specific rules, architectural decisions, or "never do this" items?

Freeform catch-all. Examples:
- "No microservices — this is a modular monolith"
- "All dates stored as UTC"
- "Don't touch the legacy billing module in /src/billing/v1"

## Presentation Format

When presenting auto-detected answers for confirmation, use this format:

```
I detected the following from your project:

  Language:    Java
  Build tool:  Gradle
  Framework:   Spring Boot
  CI system:   GitHub Actions
  Project type: Backend service (inferred from src/main/java structure)

Does this look right? Anything to correct or add?

I still need answers for:
  - Solo or team?
  - Regulated data?
  - Coverage threshold (default 95%)?
  - UI present?
  - Existing conventions to preserve?
```
