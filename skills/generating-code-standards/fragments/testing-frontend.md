<!-- tier: project -->
<!-- requires: testing-universal -->

## Frontend Testing — Shared Concepts

### E2E Browser Testing

Preferred tool: **Playwright** (cross-browser, fast, reliable).

```bash
# Install
npm init playwright@latest

# Run all E2E tests
npx playwright test

# Run with UI mode (interactive debugging)
npx playwright test --ui

# Run a specific test file
npx playwright test tests/checkout.spec.ts

# Generate HTML report
npx playwright show-report
```

Alternative: **Cypress** (simpler API, single-browser focus).

```bash
npx cypress run          # headless
npx cypress open         # interactive
```

<!-- WHY: Playwright covers Chromium, Firefox, and WebKit in one tool. Cypress is fine if already adopted, but don't introduce it for new projects. -->

### Visual Regression Testing

**Playwright built-in screenshots:**

```typescript
// In any Playwright test
await expect(page).toHaveScreenshot('homepage.png');
await expect(page.locator('.card')).toHaveScreenshot('card.png');
```

```bash
# Update snapshots after intentional visual changes
npx playwright test --update-snapshots
```

**Storybook + Chromatic** (component-level visual review):

```bash
# Run Storybook locally
npm run storybook

# Publish to Chromatic for visual diff review
npx chromatic --project-token=<token>
```

**Percy** (alternative SaaS visual testing):

```bash
npx percy exec -- npx playwright test
```

### Accessibility Testing

Run automated a11y checks as part of the test suite, not as an afterthought.

**axe-core with Playwright:**

```typescript
import AxeBuilder from '@axe-core/playwright';

test('page has no a11y violations', async ({ page }) => {
  await page.goto('/');
  const results = await new AxeBuilder({ page }).analyze();
  expect(results.violations).toEqual([]);
});
```

**Storybook a11y addon:**

```bash
npm install @storybook/addon-a11y --save-dev
```

Adds an a11y panel to every story — checks run automatically.

**pa11y (CLI / CI):**

```bash
npx pa11y http://localhost:3000
npx pa11y-ci  # runs against multiple URLs from .pa11yci config
```

### Bundle & Performance

**Lighthouse CI:**

```bash
npm install -g @lhci/cli
lhci autorun  # configured via lighthouserc.js
```

Set performance budgets:

```json
{
  "ci": {
    "assert": {
      "assertions": {
        "categories:performance": ["error", { "minScore": {{LIGHTHOUSE_PERF_SCORE}} }],
        "categories:accessibility": ["error", { "minScore": {{LIGHTHOUSE_A11Y_SCORE}} }]
      }
    }
  }
}
```

<!-- WHY: Don't hardcode these — ask the user what thresholds make sense for their project. Reasonable defaults: performance 0.9, accessibility 0.95. -->

**bundlesize / size-limit:**

```bash
# size-limit — checks JS bundle size
npx size-limit
```

### Test File Organization

```
src/
  components/
    Button/
      Button.tsx
      Button.test.tsx        # Unit/component tests — co-located
      Button.stories.tsx     # Storybook stories
tests/
  e2e/
    checkout.spec.ts         # E2E tests — separate directory
    login.spec.ts
  visual/
    screenshots/             # Baseline screenshots for regression
```

### Coverage for Frontend

Most frontend projects use the test runner's built-in coverage:

```bash
# Vitest
npx vitest --coverage

# Jest
npx jest --coverage

# Playwright (for E2E coverage via Istanbul)
# Requires instrumented build — see framework-specific fragments
```

Report location: `coverage/lcov-report/index.html` (standard for both Jest and Vitest).
