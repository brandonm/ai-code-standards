<!-- tier: project -->
<!-- requires: testing-universal, testing-frontend -->

## React Testing — Framework-Specific

### Component Testing

Use **React Testing Library** — test behavior, not implementation.

```bash
npm install --save-dev @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

```tsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { Button } from './Button';

test('calls onClick when clicked', async () => {
  const handleClick = vi.fn();
  render(<Button onClick={handleClick}>Save</Button>);

  await userEvent.click(screen.getByRole('button', { name: /save/i }));

  expect(handleClick).toHaveBeenCalledOnce();
});
```

<!-- WHY: RTL queries by role/text force you to write accessible components. If you can't find your element by role, your markup is probably wrong. -->

### What NOT to Do

- **Don't test implementation details.** No `wrapper.instance()`, no checking state directly, no shallow rendering.
- **Don't snapshot everything.** Snapshots are brittle and get blindly updated. Use them sparingly for stable output (e.g., serialized data structures), not for component HTML.
- **Don't mock everything.** Prefer rendering with real child components. Only mock at network boundaries (`msw` for API calls).

### API Mocking with MSW

```bash
npm install --save-dev msw
```

```typescript
import { http, HttpResponse } from 'msw';
import { setupServer } from 'msw/node';

const server = setupServer(
  http.get('/api/user', () => {
    return HttpResponse.json({ name: 'Brandon' });
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

<!-- WHY: MSW intercepts at the network level, so your components use their real fetch/axios calls. No coupling between tests and HTTP client implementation. -->

### Hook Testing

```tsx
import { renderHook, act } from '@testing-library/react';
import { useCounter } from './useCounter';

test('increments counter', () => {
  const { result } = renderHook(() => useCounter());

  act(() => result.current.increment());

  expect(result.current.count).toBe(1);
});
```

### Test Runner

Preferred: **Vitest** (fast, ESM-native, Vite-compatible).

```bash
npx vitest              # watch mode
npx vitest run          # single run (CI)
npx vitest --coverage   # with coverage
```

Alternative: **Jest** (if already established — don't migrate for the sake of it).

### React-Specific Storybook

```tsx
import type { Meta, StoryObj } from '@storybook/react';
import { Button } from './Button';

const meta: Meta<typeof Button> = {
  component: Button,
};
export default meta;

type Story = StoryObj<typeof Button>;

export const Primary: Story = {
  args: { variant: 'primary', children: 'Click me' },
};
```

Run interaction tests in Storybook:

```bash
npx test-storybook
```
