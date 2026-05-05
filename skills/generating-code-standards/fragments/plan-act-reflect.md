<!-- tier: global -->
<!-- requires: none -->

## Plan, Act, Reflect

<!-- WHY: Jumping straight to code for non-trivial changes leads to rework. A 5-minute plan saves hours of "oh wait, I didn't consider..." -->

### When to Plan First

Plan before acting when any of these are true:
- Change touches **3+ files**
- Adds a **new dependency**
- Modifies **authentication, authorization, or security** logic
- Changes a **public API** (endpoints, exported functions, CLI interface)
- Involves **data migration** or schema changes
- Affects **CI/CD pipeline** configuration

For single-file, single-function changes — just do it.

### Plan Format

Keep it lightweight. A plan is not a design doc. It's a short list:

```
## Plan: [what you're doing]

**Goal:** One sentence.

**Approach:**
1. Step one
2. Step two
3. Step three

**Files to change:** list them

**Risks / open questions:**
- Anything you're unsure about

**Approval needed?** Yes/No — if yes, stop and ask before proceeding.
```

### Approval Gates

Stop and ask for explicit approval before:
- Adding a new external dependency
- Changing a public API signature
- Modifying auth/security code
- Altering database schema
- Changing CI/CD configuration
- Any irreversible action

### After Acting: Reflect

After completing a non-trivial change, briefly check:
- Did the result match the plan?
- Were there surprises? (if yes, note them for next time)
- Did tests pass on the first run? (if not, why?)
- Is the diff clean? (no unrelated changes leaked in?)

This is not a formal review — it's a 30-second sanity check before calling it done.
