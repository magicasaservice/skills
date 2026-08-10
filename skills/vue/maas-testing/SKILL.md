---
name: maas-testing
description: >
  This skill should be used when writing or reviewing tests for Magic as a
  Service™ projects: component tests, composable tests, unit tests, or test
  infrastructure. Trigger on requests to "write tests", "add test coverage",
  "set up vitest", "fix a flaky test", or any work in a tests directory or
  *.test.ts file. Covers the browser-first Vitest setup, concern-split test
  files, factories with test-ID enums, and the no-mocking policy.
metadata:
  version: "1.1.0"
---

# Magic as a Service™ Testing

Clean, straightforward, no bloat. One sentence of philosophy governs
everything: **test the public contract through the DOM, in a real browser,
with hand-written assertions.** If a behavior is not observable as DOM,
attributes, or emitted events, it is not tested. Example names are
illustrative.

## Infrastructure

- **Per-package Vitest configs under ~30 lines.** No workspace file, no
  setup files, no custom matchers, no reporters. Only aliases (pointing at
  source, never `dist`) and the two settings that matter.
- **Real browser for real behavior.** Anything with layout, pointer events,
  transitions, or teleports runs in headless Chromium via Playwright browser
  mode. jsdom is reserved for pure functions and trivial DOM helpers, the
  cheap tool for the cheap job.
- Set `locators.testIdAttribute: 'data-test-id'`, and alias `vue` to the
  esm-bundler build so factories can use runtime template strings.
- **Tests gate the build, not a CI pipeline.** In `turbo.json`, `build` and
  release tasks depend on `test`; turbo caches green runs.
- **No coverage instrumentation.** Hand-written behavior assertions instead
  of a coverage number.

## Organization

- Component tests live at `<Plugin>/tests/<Plugin>.<concern>.test.ts`. The
  canonical concerns: `rendering`, `api`, `options`, `interactions`,
  `events`, `edge-cases`. A plugin with special behavior adds a concern file
  (`keyboard`, `slots`, `motion`); it never stuffs an existing one.
- Unit tests are co-located: `<function>.test.ts` next to `<function>.ts`.
  No `tests/` directory, no factories, no test IDs; import the thing and
  call it.
- Two `describe` levels maximum: `'Drawer - Options'` → one block per
  option. Test names are lowercase behavior sentences that read like docs:
  `'click again closes drawer'`, `'snapTo on closed drawer is a safe no-op'`.

## Factories and test IDs

- Every browser test file declares its **own local**
  `createDrawer(id, options = {})` factory returning a `defineComponent`
  with a template string. Duplication per file is deliberate: each factory
  carries only the markup its file needs, and nobody jumps through a helper
  hierarchy to read a test.
- **State probes over internals**: composable state is rendered into the DOM
  (`<span data-test-id="is-active">{{ isActive }}</span>`) and asserted with
  `toHaveTextContent`, never `wrapper.vm`, never exported-for-testing
  internals. Buttons wired to composable methods let tests drive the API
  through clicks.
- **Every ID comes from an enum** in `tests/enums.ts`: one *instance ID per
  test case* (grouped by concern) plus a `TestId` enum for element roles.
  Unique per-test instance IDs give isolation on top of ID-keyed state
  without any global teardown, and the enum file doubles as a table of
  contents for the suite.
- Special cases get a factory *variant* (`createDisabledDrawer`) or an
  inline `defineComponent` in the test, never option flags on a
  mega-factory.

## What we deliberately avoid

- **No `vi.mock`, ever.** No module mocks, no fake servers, no stubbed
  collaborators. `vi.fn()` only spies on real user-supplied callbacks;
  `vi.spyOn` only watches `console.warn`/`console.error`. Stubs are inert
  placeholder components for third-party children, nothing behavioral.
- **No fake timers.** Real timers, real animations, real browser. For
  rAF-driven units, hand-roll a deterministic frame clock in the test file.
- **No snapshots, no committed screenshots.** Every assertion is a
  hand-written statement about one behavior, so a failing test names the
  broken behavior. Generated artifacts are gitignored.
- **Narrow, literal assertions.** `toBe`, `toHaveTextContent`, `toBeNull`,
  `toBeCloseTo`. Assert `aria-*` as attributes; prefer
  `querySelector(...) === null` over matcher sugar.
- **Shared helpers need a reason to exist.** One shared util across a whole
  repo is a healthy number, and it carries a comment explaining exactly why
  it must exist.

## Timing without flakiness

- Primary tool: auto-retrying assertions, e.g.
  `await expect.element(page.getByTestId(...)).toHaveTextContent('true')`.
- Waits are **explicit and tuned to the real animation**: a per-file
  `open(screen)` helper wraps trigger click, `nextTick`, and a literal
  `setTimeout` matched to the transition duration. Different concern files
  tune different waits; nothing hides behind a generic `waitForAnimation()`.
- `vi.waitFor` only where `expect.element` cannot apply (spy assertions).

## Composables without a mounting library

Test composables with a ~10-line local `withSetup` helper around
`createApp`, without a component-mounting test library:

```ts
function withSetup<ResultType>(composable: () => ResultType): ResultType {
  let result!: ResultType
  app = createApp({ setup() { result = composable(); return () => null } })
  app.mount(document.createElement('div'))
  return result
}
```

## Additional resources

- `references/patterns.md`: factory, probe, enum, and timing patterns as a
  worked example
