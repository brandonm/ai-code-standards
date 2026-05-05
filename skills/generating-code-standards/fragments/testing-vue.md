<!-- tier: project -->
<!-- requires: testing-universal, testing-frontend -->

## Vue Testing — Framework-Specific

### Component Testing

Use **Vue Test Utils** with **Vitest**.

```bash
npm install --save-dev @vue/test-utils @testing-library/vue
```

**Vue Test Utils (direct mounting):**

```typescript
import { mount } from '@vue/test-utils';
import Button from './Button.vue';

test('emits click event', async () => {
  const wrapper = mount(Button, {
    props: { label: 'Save' },
  });

  await wrapper.find('button').trigger('click');

  expect(wrapper.emitted('click')).toHaveLength(1);
});
```

**Testing Library for Vue (behavior-focused):**

```typescript
import { render, screen } from '@testing-library/vue';
import userEvent from '@testing-library/user-event';
import Button from './Button.vue';

test('calls handler when clicked', async () => {
  const onClick = vi.fn();
  render(Button, {
    props: { label: 'Save', onClick },
  });

  await userEvent.click(screen.getByRole('button', { name: /save/i }));

  expect(onClick).toHaveBeenCalledOnce();
});
```

<!-- WHY: Vue Test Utils gives lower-level control (testing emitted events, slots, provide/inject). Testing Library enforces accessible queries. Use whichever fits the test — they're complementary. -->

### Pinia Store Testing

```typescript
import { setActivePinia, createPinia } from 'pinia';
import { useUserStore } from './userStore';

beforeEach(() => {
  setActivePinia(createPinia());
});

test('updates user name', () => {
  const store = useUserStore();
  store.setName('Brandon');
  expect(store.name).toBe('Brandon');
});
```

### Composable Testing

```typescript
import { ref } from 'vue';
import { useCounter } from './useCounter';

test('increments counter', () => {
  const { count, increment } = useCounter();
  increment();
  expect(count.value).toBe(1);
});
```

If a composable requires a component context (lifecycle hooks, provide/inject), wrap with `withSetup`:

```typescript
import { mount } from '@vue/test-utils';
import { defineComponent } from 'vue';

function withSetup<T>(composable: () => T): T {
  let result: T;
  mount(defineComponent({
    setup() {
      result = composable();
      return () => {};
    },
  }));
  return result!;
}
```

### Test Runner

Use **Vitest** — it shares the Vite config, so aliases and transforms just work.

```bash
npx vitest              # watch mode
npx vitest run          # single run (CI)
npx vitest --coverage   # with coverage
```

### Vue-Specific Storybook

```typescript
import type { Meta, StoryObj } from '@storybook/vue3';
import Button from './Button.vue';

const meta: Meta<typeof Button> = {
  component: Button,
};
export default meta;

type Story = StoryObj<typeof Button>;

export const Primary: Story = {
  args: { variant: 'primary', label: 'Click me' },
};
```
