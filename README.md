# ai-code-standards

Generate tailored engineering standards files for AI coding assistants. Instead of copy-pasting rules or maintaining a single static file, this skill composes modular fragments based on your project's stack, domain, team size, and regulatory requirements.

## What It Does

Generates a comprehensive standards file (e.g., `CLAUDE.md`) for your project by:

1. **Detecting** your stack from manifest files (`package.json`, `build.gradle`, `go.mod`, etc.)
2. **Asking** a short questionnaire to fill in what can't be auto-detected
3. **Composing** modular fragments into a single, tailored standards file
4. **Merging** with any existing standards file — never clobbering your additions

## Supported Stacks

| Language | Build Tool | Testing Fragment |
|----------|-----------|-----------------|
| Java | Gradle | JaCoCo coverage, SpotBugs, OWASP dependency check |
| Go | go build | go test, cargo-llvm-cov/tarpaulin, gosec, govulncheck |
| Python | pip | pytest, coverage.py, bandit, pip-audit, mypy |
| TypeScript/JavaScript | npm | Vitest/Jest, c8/v8, eslint-plugin-security |
| Rust | cargo | cargo test, cargo-llvm-cov/tarpaulin, clippy, cargo audit |

### Frontend Frameworks

| Framework | Testing Fragment |
|-----------|-----------------|
| React / Next.js | React Testing Library, MSW, Storybook |
| Vue / Nuxt | Vue Test Utils, Testing Library for Vue, Pinia, Storybook |
| Shared | Playwright E2E, visual regression, axe-core a11y, Lighthouse CI |

### Multi-Stack Projects

Full-stack projects (e.g., Go + React, Java + Vue) are fully supported. Each stack gets its own testing and SAST fragments, and the generated file covers both.

## What's Included

Every generated standards file includes these sections:

| Section | What it covers |
|---------|---------------|
| **Honesty & Uncertainty** | No fabrication, verify before answering, admit uncertainty |
| **Boundaries** | Hard "never do" rules — destructive ops, security, scope creep, dependency safety |
| **Plan, Act, Reflect** | When to plan first, approval gates, post-action sanity check |
| **Testing Standards** | Coverage gates, testing types, per-stack commands and config |
| **Code Review Gate** | Self-review checklist, AI code review, security scan, peer review |
| **Security Scanning** | Secrets detection, SAST, SCA, input validation, auth rules |
| **Git Hygiene** | Conventional Commits, atomic commits, branch strategy, PR rules |
| **Definition of Done** | Checklist that blocks "done" until all gates pass |

### Optional Sections (based on your inputs)

| Section | When included |
|---------|--------------|
| **Regulated Data** | PII, PHI, PCI, or government data handling |
| **Accessibility** | UI-facing projects (WCAG 2.1 AA) |
| **Data Privacy** | Projects handling PII or PHI |
| **Solo Guardrails** | Relaxed process rules for solo developers |
| **Team Guardrails** | Branch protection, CODEOWNERS, communication rules |

## Installation

### Claude Code (via marketplace)

Two-step install — add the marketplace, then install the plugin:

```bash
# Step 1: Add the marketplace
claude plugin marketplace add brandonm/ai-code-standards

# Step 2: Install the plugin
claude plugin install ai-code-standards
```

### Manual

Clone the repo and copy the `project-standards/` directory to your skills location:

```bash
git clone https://github.com/brandonm/ai-code-standards.git
mkdir -p ~/.claude/skills
cp -r ai-code-standards/project-standards ~/.claude/skills/project-standards
```

## Usage

Once installed, invoke the skill in Claude Code:

```
/project-standards
```

Or ask naturally:

> "Generate engineering standards for this project"
> "Create a CLAUDE.md for this repo"
> "Set up project standards"

The skill will:
1. Auto-detect your stack from manifest files
2. Ask you to confirm and fill in missing details
3. Generate a tailored standards file
4. Show you the output for review before writing

## Project Structure

```
project-standards/
  SKILL.md                         # Skill entry point and workflow
  questionnaire.md                 # Questions to gather inputs
  template.md                      # Assembly skeleton with conditionals
  fragments/
    honesty.md                     # Uncertainty protocol
    boundaries.md                  # Do-not-do list
    boundaries-solo.md             # Relaxed rules for solo devs
    boundaries-team.md             # Stricter rules for teams
    testing-universal.md           # Testing philosophy and gates
    testing-java-gradle.md         # JaCoCo, Gradle commands
    testing-go.md                  # go test, coverage
    testing-python.md              # pytest, coverage.py
    testing-node.md                # Vitest/Jest, c8
    testing-rust.md                # cargo test, llvm-cov
    testing-frontend.md            # Playwright, visual regression, a11y
    testing-react.md               # React Testing Library, MSW
    testing-vue.md                 # Vue Test Utils, Pinia
    review-gate.md                 # Self-review + AI review checklist
    security-generic.md            # Secrets, SAST, SCA, auth
    security-regulated.md          # PII/PHI/PCI additions
    plan-act-reflect.md            # Workflow for non-trivial changes
    git-hygiene.md                 # Commit and branch conventions
    ui-accessibility.md            # WCAG 2.1 AA rules
    data-privacy.md                # Data handling and retention
  examples/
    java-gradle-backend.md         # Single-stack backend example
    java-react-fullstack.md        # Multi-stack full-stack example
    python-data-pipeline.md        # Solo dev, data pipeline example
    typescript-react-app.md        # Frontend web app example
```

## Extending

### Adding a new stack

1. Create a new fragment at `fragments/testing-<language>.md`
2. Use `<!-- tier: project -->` and `<!-- requires: testing-universal -->`
3. Include: test commands, coverage commands, report locations, test organization, SAST tools
4. Add the fragment to the selection matrix in `SKILL.md`
5. Add a conditional to `template.md`

### Adding a new domain

1. Create a new fragment at `fragments/<domain>.md`
2. Add a question to `questionnaire.md` to trigger inclusion
3. Add a conditional to `template.md`

### Modifying rules

Each fragment is standalone and order-independent. Edit any fragment directly — changes apply to all future generations. Existing generated files are not affected until regenerated.

## Design Principles

- **Simple, explicit, boring** — no clever meta-logic, mostly substitution and concatenation
- **Composable fragments** — standalone, order-independent, each works if included alone
- **Include "why" comments** — prevent cargo-culting by explaining the reason behind each rule
- **Detect before asking** — auto-detect what we can, confirm with the user
- **Never clobber** — always check for existing files, merge rather than overwrite
- **Verify commands** — every command in a stack fragment must be real, never invented

## Contributing

Contributions welcome. If you add a stack, domain, or improve an existing fragment:

1. Follow the existing fragment format (tier marker, requires marker, WHY comments)
2. Keep fragments under 50 lines where possible
3. Include only verified, real commands — no hallucinated flags
4. Add or update an example showing the fragment in context

## License

MIT
