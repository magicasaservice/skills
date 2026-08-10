# Testing patterns: worked example

A drawer plugin illustrates the patterns; names are illustrative.

## Browser config (the whole thing)

```ts
import vue from '@vitejs/plugin-vue'
import { defineConfig } from 'vitest/config'
import { playwright } from '@vitest/browser-playwright'

export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: [
      // Factories use runtime template strings
      { find: 'vue', replacement: 'vue/dist/vue.esm-bundler.js' },
    ],
  },
  test: {
    browser: {
      enabled: true,
      provider: playwright(),
      headless: true,
      instances: [{ browser: 'chromium' }],
      locators: { testIdAttribute: 'data-test-id' },
    },
    include: ['*/tests/**/*.test.ts'],
    globals: true,
  },
})
```

## Test-ID enums

`tests/enums.ts` holds one instance ID per test case, grouped by concern, plus
reusable element roles:

```ts
export enum DrawerId {
  // rendering
  RenderTrigger = 'render-trigger',
  RenderTriggerActive = 'render-trigger-active',
  // options
  PositionTop = 'position-top',
  SnapPercent = 'snap-percent',
  // interactions
  IntBackdropClick = 'int-backdrop-click',
}

export enum TestId {
  Trigger = 'trigger',
  CloseBtn = 'close-btn',
  IsActive = 'is-active',
  DrawerContent = 'drawer-content',
}
```

## The per-file factory with state probes

```ts
function createDrawer(id: DrawerId, options: Record<string, unknown> = {}) {
  return defineComponent({
    components: {
      DrawerProvider,
      DrawerTrigger,
      DrawerTeleport,
      DrawerBackdrop,
      DrawerContent,
    },
    setup() {
      const { open, close, isActive, progress } = useDrawer(id)
      return { open, close, isActive, progress }
    },
    template: `
      <DrawerProvider id="${id}" :options="options">
        <span data-test-id="${TestId.IsActive}">{{ isActive }}</span>
        <button data-test-id="${TestId.Trigger}" @click="open">Open</button>
        <button data-test-id="${TestId.CloseBtn}" @click="close">Close</button>
        <DrawerTeleport>
          <DrawerBackdrop />
          <DrawerContent>
            <div data-test-id="${TestId.DrawerContent}" style="height: 200px;">
              Content
            </div>
          </DrawerContent>
        </DrawerTeleport>
      </DrawerProvider>
    `,
    data() {
      return { options }
    },
  })
}
```

Everything is interpolated from enums; `options` goes through `data()` so
`:options="options"` is a real reactive binding.

## A typical test

```ts
describe('Drawer - API', () => {
  describe('open and close', () => {
    it('click again closes drawer', async () => {
      const screen = render(createDrawer(DrawerId.ApiToggle))

      await open(screen)
      await expect
        .element(page.getByTestId(TestId.IsActive))
        .toHaveTextContent('true')

      await screen.getByTestId(TestId.Trigger).click()
      await expect
        .element(page.getByTestId(TestId.IsActive))
        .toHaveTextContent('false')
    })
  })
})
```

Locators act and auto-retry; raw `querySelector` reads classes and
attributes:

```ts
expect(document.querySelector('.drawer-content')).toBeNull()
expect(el.getAttribute('data-position')).toBe('top')
```

## The per-file timing helper

Waits are literal and tuned to the real transition of the behavior under
test; each concern file owns its own numbers:

```ts
async function open(screen: ReturnType<typeof render>) {
  await screen.getByTestId(TestId.Trigger).click()
  await nextTick()
  await nextTick()
  await new Promise((resolve) => setTimeout(resolve, 350))
}
```

## Deterministic frame clock instead of fake timers

For rAF-driven units in jsdom:

```ts
const rafCallbacks = new Map<number, FrameRequestCallback>()
let rafId = 0

function tick(timestamp: number) {
  const pending = [...rafCallbacks.values()]
  rafCallbacks.clear()
  pending.forEach((fn) => fn(timestamp))
}

beforeEach(() => {
  rafCallbacks.clear()
  rafId = 0
  vi.stubGlobal('requestAnimationFrame', (fn: FrameRequestCallback) => {
    const id = ++rafId
    rafCallbacks.set(id, fn)
    return id
  })
})

afterEach(() => {
  vi.unstubAllGlobals()
})
```

## Build gating

```json
{
  "tasks": {
    "test": { "cache": true, "outputs": [] },
    "build": { "outputs": ["./dist/**"], "dependsOn": ["test"] }
  }
}
```
