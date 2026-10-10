---
name: maas-vue-conventions
description: >
  This skill should be used when writing or editing Vue single-file
  components or composables in Magic as a Service™ projects, day-to-day
  component code. Trigger on requests to "write/edit a component", "add
  props", "set prop defaults", "create a composable", "add an event
  listener", "add a transition", or changes to any .vue file. Covers props
  declarations, reactive state, composable anatomy, and transition
  conventions.
metadata:
  version: "1.2.0"
---

# Magic as a Service™ Vue Conventions

Code-level rules for writing Vue SFCs and composables. For *designing*
components and modules (state sharing, APIs, styling contracts) see
`maas-component-architecture`. Example names are illustrative.

## Declaring props

- Props are a TypeScript `interface` named after the file:
  `DrawerTrigger.vue` → `interface DrawerTriggerProps`.
- Destructure in `defineProps`, with defaults set in the destructure (Vue 3.5
  reactive props destructure):
  `const { size = 'md' } = defineProps<ExampleProps>()`.
- **Never use `withDefaults`.** The destructure form replaces it entirely.
- Exception: when the prop type is an imported library or shared type
  (`NodeProps` from `@vue-flow/core`, a shared `EdgeProps`), use it directly;
  don’t rename it to `<Filename>Props`. The naming rule applies to
  locally-defined interfaces.

## Prefer `shallowRef`

- Reactive state is a `shallowRef` by default, whatever it holds:
  `const isOpen = shallowRef(false)`,
  `const items = shallowRef<Array<Item>>([])`.
- With a `shallowRef`, update objects and arrays by assigning a new value
  (`items.value = [...items.value, item]`); mutating them in place triggers
  nothing.
- Use `ref` only when deep reactivity is actually used: nested state is
  mutated in place (`items.value.push(item)`, `state.value.refCount++`) and
  the UI has to follow.
- `ref` wraps every object it holds in a deep proxy and tracks each nested
  read; `shallowRef` tracks `.value` alone.

## Composable anatomy

Every composable follows the same internal layout:

- Sub-composable dependencies sit at the top, right after the args
  destructure.
- Members are grouped under fixed section comments (`// Private state`,
  `// Public state`, `// Private functions`, `// Public functions`), with
  groups that don’t apply omitted. These grouping comments are the one
  sanctioned exception to the minimal-comments rule in `maas-conventions`.
- Watchers and lifecycle hooks (`onScopeDispose`) come after the functions,
  before the return.
- Event listeners use VueUse’s `useEventListener(target, event, handler)`,
  never raw `addEventListener`/`removeEventListener`. Store the returned stop
  function to detach; keep `onScopeDispose` only for extra teardown, such as
  cancelling a pending `requestAnimationFrame`.
- Export the return type at the bottom:
  `export type UseDrawerReturn = ReturnType<typeof useDrawer>`.

## Transitions are named classes

When a component’s transitions need per-caller configuration, the options
object carries Vue transition **names**, not timing values:

- Options map parts to transition names
  (`transition: { content: 'my-drawer-content' }`) with defaults matching the
  component’s built-in class names; consumers override by supplying their own
  names and defining the `*-enter-active`/`*-leave-active` classes in their
  own non-scoped `<style>` block.
- Never pass durations or delays as numbers that become CSS variables. All
  timing and easing lives in CSS under the named classes; the JS/CSS
  boundary stays clean and the whole choreography is co-located in one style
  block.
