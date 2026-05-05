<!-- tier: project -->
<!-- requires: testing-universal -->

## Node/TypeScript Testing — Stack-Specific Commands

### Running Tests

**Vitest (preferred):**

```bash
# Run all tests
npx vitest run

# Watch mode (local dev)
npx vitest

# Run a specific test file
npx vitest run src/utils/parser.test.ts

# Run tests matching a pattern
npx vitest run -t "should parse"
```

**Jest (if already established):**

```bash
# Run all tests
npx jest

# Watch mode
npx jest --watch

# Run a specific file
npx jest src/utils/parser.test.ts
```

<!-- WHY: Vitest is faster, ESM-native, and shares Vite config if you're using it. Don't migrate from Jest for the sake of it, but prefer Vitest for new projects. -->

### Coverage

**Vitest (uses c8/v8 built-in):**

```bash
# Run with coverage
npx vitest run --coverage

# Fail if below threshold (configure in vitest.config.ts)
```

Configure thresholds in `vitest.config.ts`:

```typescript
export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
      thresholds: {
        lines: {{COVERAGE_THRESHOLD}},
        branches: {{COVERAGE_THRESHOLD}},
      },
      reporter: ['text', 'html', 'lcov'],
    },
  },
});
```

**Jest (uses istanbul/nyc):**

```bash
npx jest --coverage
```

Configure thresholds in `jest.config.ts`:

```typescript
export default {
  coverageThreshold: {
    global: {
      lines: {{COVERAGE_THRESHOLD}},
      branches: {{COVERAGE_THRESHOLD}},
    },
  },
  coverageReporters: ['text', 'html', 'lcov'],
};
```

### Report Locations

| Runner | Format | Path |
|--------|--------|------|
| Vitest | HTML | `coverage/index.html` |
| Vitest | lcov | `coverage/lcov.info` |
| Jest | HTML | `coverage/lcov-report/index.html` |
| Jest | lcov | `coverage/lcov.info` |

### Test Organization

```
src/
  utils/
    parser.ts
    parser.test.ts          # Co-located — preferred
  services/
    auth.ts
    auth.test.ts
tests/
  integration/
    api.test.ts             # Integration tests — separate directory
    db.test.ts
```

<!-- WHY: Co-located tests (foo.test.ts next to foo.ts) are easier to find and maintain than a mirrored __tests__/ tree. Reserve a separate tests/ directory for integration tests that span multiple modules. -->

### API Mocking with MSW

For testing HTTP clients and API integrations without coupling to implementation:

```bash
npm install --save-dev msw
```

```typescript
import { http, HttpResponse } from 'msw';
import { setupServer } from 'msw/node';

const server = setupServer(
  http.get('/api/users', () => {
    return HttpResponse.json([{ id: 1, name: 'Test' }]);
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

### SAST for Node

```bash
# Dependency vulnerability check
npm audit

# With yarn
yarn audit

# ESLint security plugin
npx eslint --plugin security src/

# Semgrep (multi-language, includes JS/TS rules)
semgrep --config=auto src/
```
