<!-- tier: global -->
<!-- requires: none -->

## Testing Standards

<!-- WHY: Tests are the only proof that code works. Without enforced coverage gates, coverage erodes one "I'll add tests later" at a time. -->

### Non-Negotiable Rules

1. **Tests ship with code.** Every PR that adds or changes behavior includes tests for that behavior. No exceptions, no "test ticket" follow-ups.

2. **Coverage gate: {{COVERAGE_THRESHOLD}}% line + branch.** Measured per package/module, not just aggregate. A 98% average can hide a 40% package.

3. **Every public function/method is tested.** If it's exported, it has at least one test. If it has branching logic, it has tests for each branch.

4. **Tests must be deterministic.** No flaky tests. No time-dependent tests. No tests that pass only on your machine. If a test is flaky, fix it or delete it — do not retry-loop it.

5. **Tests must be fast.** Unit tests run in seconds, not minutes. If a test needs a database, network, or filesystem, it's an integration test — label it accordingly.

### Testing Types

<!-- Include sections below based on questionnaire Q7 answers -->

#### Unit Tests
- Test one function/method in isolation
- Mock external dependencies (DB, HTTP, filesystem)
- Run on every commit — must complete in under 60 seconds total
- Live alongside the code they test

#### Integration Tests
- Verify components work together with real dependencies
- Use test containers or in-memory substitutes where practical
- Run on every PR — acceptable to take minutes
- Clearly separated from unit tests (separate directory or build tag)

#### End-to-End Tests
- Simulate real user workflows through the full stack
- Run against a staging environment or local docker-compose
- Run before merge to main — acceptable to take longer
- Cover critical paths: login, core CRUD, payment flows

#### Contract Tests
- Verify API contracts between producer and consumer services
- Run on both sides independently
- Break the build if contracts drift

#### Performance / Load Tests
- Establish baseline metrics before changes
- Run on a schedule or before releases, not every PR
- Alert on regressions beyond defined thresholds

#### Property-Based Tests
- Use for functions with wide input domains (parsers, serializers, validators)
- Complement unit tests, don't replace them
- Set a reasonable iteration count (100-1000 cases)

#### UI / Visual Tests
- Test component rendering and interaction behavior
- Use screenshot comparison for visual regression
- Run on PR — flag visual diffs for human review

### Definition of Done for Tests

A test is not "done" unless:
- [ ] It fails when the code under test is broken (invert assertion to verify)
- [ ] It has a clear name describing the scenario and expected outcome
- [ ] It tests behavior, not implementation details
- [ ] It cleans up after itself (no leaked state between tests)
