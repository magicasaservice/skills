---
name: maas-component-architecture
description: >
  This skill should be used when designing or building Vue/Nuxt components,
  plugins, composables, or component libraries for Magic as a Service™, and
  when deciding how components share state, communicate, or expose their API.
  Trigger on requests to "build a component", "create a plugin/composable",
  "share state between components", "add events/callbacks", or questions
  about component APIs, state management, or component communication. Covers
  the standalone/module distinction, ID-keyed state composables,
  emitter-based communication, inversion of control, styling APIs, and
  bundled options.
metadata:
  version: "1.1.1"
---

# Magic as a Service™ Component Architecture

How we design interactive components and component libraries. The common
thread: **components must not depend on their position in the component
tree.** Everything below serves that goal. Example names (a drawer module)
are illustrative.

## Standalone components and modules

Every component is one of two kinds, and the choice decides which patterns
below apply:

- **A standalone component** is self-contained: one SFC (plus at most small
  internal children), local state, and an API of props, emits, and slots.
  No shared store, no module folder. Buttons, badges, inputs, cards.
- **A module** encapsulates many interdependent components and composables:
  a provider/trigger/content set, a multi-part widget. It bundles its
  components, private composables, machines, types, and symbols; internals
  are reachable only from inside. **One composable is the entire public
  API**: consumers interact with the module exclusively through
  `useDrawer(id)`; state, commands, and events all flow through it. The
  components are markup; the composable is the API.

The deciding question: do separate parts need to coordinate, or does
anything need to reach this component from elsewhere in the tree? Then it is
a module. Start standalone; promote to a module when the second coordinated
part appears, never preemptively.

Modules never import another module’s internals, and there is no second
entry point. If two modules must coordinate, that happens in the app’s
control plane (see `maas-nuxt-architecture`).

## ID-keyed state composables

The state mechanism for modules. Components can natively only reach state in
themselves, their parents, or their children. Instead of Pinia (dependency
bloat) or prop drilling, shared state lives in a store *outside* the
component tree, keyed by a unique ID:

- State and ID are tightly coupled: `useDrawerState(id)` finds or creates the
  state for that ID. Any component, anywhere in the tree, calling it with the
  same ID gets the same reactive object.
- The store is scoped to the Vue app instance (not module scope) so SSR
  requests never leak state into each other.
- State is **ref-counted**: each consuming scope increments a counter on
  initialize and decrements it via `onScopeDispose`. When the count reaches
  zero the state is deleted. Lifetime follows usage: no global singletons, no
  leaks.
- Recommend enums for IDs so producers and consumers cannot drift apart.

Full pattern with code: `references/patterns.md`.

## Emitter-based communication

Cross-cutting communication runs over one app-wide, fully typed event emitter
(mitt), itself just another ID-keyed store, instead of callback props or
direct method calls:

- Components emit their lifecycle as events (`beforeEnter`, `enter`,
  `afterEnter`, `progress`, …) with the instance ID in the payload.
- Producers never know who is listening. A menu can react to a player, a
  toast to a form; no shared ancestor required.
- The event stream doubles as the public API: documented events, not ad-hoc
  callbacks.

## Inversion of control

For standalone components and modules alike: the library provides behavior;
the consumer stays in charge of markup, styling, and composition:

- A provider component supplies the instance ID via injection; every child
  resolves `id ?? injectedInstanceId`. Each part therefore works nested inside
  the provider *or* standalone with an explicit ID.
- Polymorphic rendering (`asChild` via a shared primitive component): the
  consumer can replace the rendered element entirely; the component only
  contributes behavior. Slots expose internal state as slot props.
- Rendering is composed through slots, never toggled through props. A
  boolean prop or option that shows or hides a part of a component
  (`closeButton`, `showHeader`) is a render decision smuggled into the
  configuration API. Instead, expose the part as a named scoped slot and
  ship the default markup as its own small component the consumer drops
  into the slot. Slot props carry the state and actions the content needs.
  Do not put the default markup in slot fallback content when the consumer
  must be able to render nothing: Vue falls back whenever provided slot
  content renders only comment nodes, so an override with a false `v-if`
  resurrects the fallback instead of suppressing the part. Leave such
  slots bare.
- Ship unstyled: only functionally required CSS. Everything visual goes
  through the styling API below.
- Expose injection keys and types so consumers can swap out whole parts.
- Fail loudly when the contract is broken: assert required context with a
  clear message and a snake_case error code, documented one-for-one in the
  docs.

## Styling API: data attributes and CSS variables

CSS is the public styling surface; components never expose style props or
state classes:

- **State lives in data attributes.** Components surface their state as
  `data-*` attributes (`data-active`, `data-disabled`, `data-dragging`).
  They are the observability contract *and* the styling hooks: consumers and
  the component itself style with attribute selectors
  (`[data-dragging='true']`), never with toggled state classes. Bind
  optional attributes with `:data-focused="focused || null"` so false
  renders as absent.
- **Public CSS variables are the theme contract.** Namespaced by component
  (`--drawer-height`, `--drawer-trigger-cursor`), always consumed with a
  fallback that doubles as the default
  (`var(--drawer-trigger-cursor, pointer)`), and documented one-for-one in
  the component’s CSS Variables docs section. Consumers override them
  freely.
- **Internal CSS variables collapse the namespace to initials.** Public
  variables carry the full component name; internal ones abbreviate it. For
  a `side-sheet` component, `--side-sheet-height` is public while
  `--ss-dragged-y` is internal: undocumented, free to change, never for
  consumers to set. The abbreviated prefix keeps internals visibly distinct
  from the public contract.
- **Dynamic values cross from script to style as component-local variables**,
  set via a computed style binding, instead of inline style properties; the
  stylesheet stays the single place where visual rules live.

## Separation of concerns

Every pattern above depends on concerns staying cleanly scoped; nothing
cross-pollutes:

- **One composable per concern.** A drawer module’s logic splits into private
  composables named by what they own: `useDrawerState`, `useDrawerDrag`,
  `useDrawerSnap`, `useDrawerWheel`, `useDrawerProgress`,
  `useDrawerCallback`. Drag logic never computes snap points; snap logic
  never touches the wheel handling. When a new concern appears, it gets a
  new composable; existing ones don’t grow sideways.
- **One public façade.** The private composables stay private; consumers get
  a single `useDrawer` composable that composes them. Internal refactors
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

Module configuration is one `options` object on the outermost component, not
a prop per setting (standalone components keep ordinary props):

- Nested components read resolved options from the shared state: no prop
  repetition down the tree, cleaner templates.
- Options are deep-merged over a single `defaultOptions` object (defu), with
  deliberate array-replacement overrides where merging would be wrong.
- Direct props on nested components remain as targeted escape hatches and win
  over options: `disabled ?? state.options.disabled`.

## Related

Code-level rules for writing the actual SFCs and composables (props
declarations, composable layout, transitions) live in
`maas-vue-conventions`.

## Additional resources

- `references/patterns.md`: the patterns as a worked example
