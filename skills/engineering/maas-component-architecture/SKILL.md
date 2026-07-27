---
name: maas-component-architecture
description: >
  This skill should be used when designing or building Vue/Nuxt components,
  plugins, composables, or component libraries for Magic as a Service™ — and
  when
  deciding how components share state, communicate, or expose their API.
  Trigger on requests to "build a component", "create a plugin/composable",
  "share state between components", "add events/callbacks", or questions about
  props design, state management, or component communication. Covers ID-keyed
  state composables, emitter-based communication, inversion of control, and
  bundled options.
metadata:
  version: "1.0.0"
---

# Magic as a Service™ Component Architecture

The design ideas behind Vue Equipment (vue.equipment) — apply them to any
component or library built at Magic as a Service™. Reference:
https://maas.engineering/posts/vue-equipment-decoupling-with-state-composables

The common thread: **components must not depend on their position in the
component tree.** Everything below serves that goal.

## ID-keyed state composables

Components can natively only reach state in themselves, their parents, or
their children. Instead of Pinia (dependency bloat) or prop drilling, shared
state lives in a store *outside* the component tree, keyed by a unique ID:

- State and ID are tightly coupled: `useXState(id)` finds or creates the state
  for that ID. Any component, anywhere in the tree, calling it with the same
  ID gets the same reactive object.
- The store is scoped to the Vue app instance (not module scope) so SSR
  requests never leak state into each other.
- State is **ref-counted**: each consuming scope increments a counter on
  initialize and decrements it via `onScopeDispose`. When the count reaches
  zero the state is deleted. Lifetime follows usage — no global singletons, no
  leaks.
- Recommend enums for IDs so producers and consumers cannot drift apart.

Full pattern with code: `references/patterns.md`.

## Emitter-based communication

Cross-cutting communication runs over one app-wide, fully typed event emitter
(mitt) — itself just another ID-keyed store — instead of callback props or
direct method calls:

- Components emit their lifecycle as events (`beforeEnter`, `enter`,
  `afterEnter`, `progress`, …) with the instance ID in the payload.
- Producers never know who is listening. A menu can react to a player, a
  toast to a form — no shared ancestor required.
- The event stream doubles as the public API: documented events, not ad-hoc
  callbacks.

## Inversion of control

The library provides behavior; the consumer stays in charge of markup,
styling, and composition:

- A provider component supplies the instance ID via injection; every child
  resolves `id ?? injectedInstanceId`. Each part therefore works nested inside
  the provider *or* standalone with an explicit ID.
- Polymorphic rendering (`asChild` via vue-primitive): the consumer can
  replace the rendered element entirely; the component only contributes
  behavior. Slots expose internal state as slot props.
- Ship unstyled: only functionally required CSS. State is surfaced as data
  attributes (`data-active`, `data-disabled`) and every visual value is a CSS
  variable with a fallback — CSS is the styling API.
- Expose injection keys and types so consumers can swap out whole parts.
- Fail loudly when the contract is broken: assert required context with a
  clear message and a snake_case error code, documented one-for-one in the
  docs.

## Separation of concerns

Every pattern above depends on concerns staying cleanly scoped — nothing
cross-pollutes:

- **One composable per concern.** MagicDrawer’s logic is split into private
  composables named by what they own: `useDrawerState`, `useDrawerDrag`,
  `useDrawerSnap`, `useDrawerWheel`, `useDrawerProgress`,
  `useDrawerCallback`. Drag logic never computes snap points; snap logic
  never touches the DOM wheel handling. When a new concern appears, it gets a
  new composable — existing ones don’t grow sideways.
- **One public façade.** The private composables stay private; consumers get
  a single `useMagicX` composable that composes them. Internal refactors
  never leak into the public API.
- **Concerns meet at the state object.** The ID-keyed state is the one shared
  seam: each concern reads and writes the fields it owns. No composable calls
  another concern’s functions directly; cross-cutting notifications go
  through the emitter.
- **The split extends outward.** Packages separate by kind (plugins,
  composables, utils), components by role (Provider, Trigger, Content), tests
  by concern (api, rendering, options, events, interactions, edge-cases).
  The same question decides every boundary: who owns this?

## Bundled options instead of props

Configuration is one `options` object on the outermost component, not a prop
per setting:

- Nested components read resolved options from the shared state — no prop
  repetition down the tree, cleaner templates.
- Options are deep-merged over a single `defaultOptions` object (defu), with
  deliberate array-replacement overrides where merging would be wrong.
- Direct props on nested components remain as targeted escape hatches and win
  over options: `disabled ?? state.options.disabled`.

## Declaring props

- Props are a TypeScript `interface` named after the file:
  `MagicDrawerTrigger.vue` → `interface MagicDrawerTriggerProps`.
- Destructure in `defineProps`, with defaults set in the destructure (Vue 3.5
  reactive props destructure):
  `const { size = 'md' } = defineProps<ExampleProps>()`.
- **Never use `withDefaults`.** The destructure form replaces it entirely.
- Exception: when the prop type is an imported library or shared type
  (`NodeProps` from `@vue-flow/core`, a shared `EdgeProps`), use it directly —
  don’t rename it to `<Filename>Props`. The naming rule applies to
  locally-defined interfaces.

## Composable anatomy

Every composable follows the same internal layout:

- Sub-composable dependencies sit at the top, right after the args
  destructure.
- Members are grouped under fixed section comments — `// Private state`,
  `// Public state`, `// Private functions`, `// Public functions` — with
  groups that don’t apply omitted. These grouping comments are the one
  sanctioned exception to the minimal-comments rule in `maas-conventions`.
- Watchers and lifecycle hooks (`onScopeDispose`) come after the functions,
  before the return.
- Event listeners use VueUse’s `useEventListener(target, event, handler)` —
  never raw `addEventListener`/`removeEventListener`. Store the returned stop
  function to detach; keep `onScopeDispose` only for extra teardown, such as
  cancelling a pending `requestAnimationFrame`.

## Transitions are named classes

When a component’s transitions need per-caller configuration, the options
object carries Vue transition **names**, not timing values:

- Options map parts to transition names
  (`transition: { content: 'my-drawer-content' }`) with defaults matching the
  component’s built-in class names; consumers override by supplying their own
  names and defining the `*-enter-active`/`*-leave-active` classes in their
  own non-scoped `<style>` block.
- Never pass durations or delays as numbers that become CSS variables. All
  timing and easing lives in CSS under the named classes — the JS/CSS
  boundary stays clean and the whole choreography is co-located in one style
  block.

## Additional resources

- `references/patterns.md` — the patterns with real code from Vue Equipment
