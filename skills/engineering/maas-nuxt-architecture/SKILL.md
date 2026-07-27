---
name: maas-nuxt-architecture
description: >
  This skill should be used when structuring Nuxt applications or layers for
  Magic as a Service™ — creating a feature, deciding where code lives, wiring
  features together, or adding instance IDs. Trigger on requests to "add a
  feature/module", "structure the app", "connect/wire components", or any
  work inside a Nuxt layer or app/modules directory. Covers feature modules,
  the control-plane pattern, instance ID enums, and data-attribute contracts.
metadata:
  version: "1.0.0"
---

# Magic as a Service™ Nuxt Architecture

How Nuxt apps are structured at scale, distilled from internal product
codebases. Four ideas, all serving one goal: features stay isolated;
coupling is explicit and lives in known places. All example names below are
illustrative.

## Features are local Nuxt modules

Every feature is a real Nuxt module at `app/modules/<PascalName>/`,
registered by path in the layer’s `nuxt.config.ts`:

```
modules/OmniBar/
├── index.ts                   # defineNuxtModule: addComponentsDir + addImports
├── components/                # flat, pathPrefix: false, fully-prefixed names
├── composables/useOmniBar.ts  # the public API
├── composables/private/       # internals — never added to addImports
├── machines/                  # state machines
├── symbols/                   # InjectionKeys
├── types/  utils/
```

- **`composables/private/` is the encapsulation boundary.** Only non-private
  composables get `addImports`; everything else is reachable only from inside
  the module.
- **`addComponentsDir` always uses `pathPrefix: false`** — component
  filenames carry the full prefix themselves
  (`OmniBarSearchViewResultList.vue`). Deep nesting is by domain subfolder
  with flat prefixed names.
- **Modules never import each other’s internals.** Cross-module knowledge is
  banned at this level — it lives in the control plane.

## The control plane

Cross-module wiring lives in exactly one app-level composable (for example
`useControlPlane()`, called once from `app.vue`). It is the only place where
modules learn about each other:

- It obtains each module’s public API by instance ID and subscribes to the
  shared lifecycle emitter (`enter` / `afterEnter` / `leave` / `afterLeave`,
  payload = instance ID), dispatching via `switch`.
- Route-driven chrome reconciliation happens here (`route.name` → a burst of
  machine events), not per page.
- Global hotkeys live here, not in components.
- Handlers are detached explicitly (`emitter.off`) on unmount.

A module that needs to react to another module is a design smell — the
reaction belongs in the control plane, keeping both modules reusable.

## Instance IDs are enums

Every instance ID in the system lives in central TS enums, value scheme
`kind--name`:

```ts
export enum DrawerIds {
  Settings = 'drawer--settings',
  Sidebar = 'drawer--sidebar',
}
```

Usage is always `useMagicDrawer(DrawerIds.Settings)` — raw ID strings never
appear inline. Module-scoped IDs get their own `utils/enums.ts` inside the
module. This is the one sanctioned use of `enum`; elsewhere prefer
`as const` unions.

## Data-attribute contracts

When DOM behavior must span modules (click-outside, dismiss-on-press),
components never reference each other’s selectors. Behavior is negotiated
through documented `data-*` attributes, always exported as an
Attribute + Selector pair:

```ts
// Persistent chrome surfaces tag their root so click-outside handlers
// elsewhere can ignore "clicked other chrome" without naming every surface.
export const ClickOutsideChromeAttribute = 'data-click-outside-chrome'
export const ClickOutsideChromeSelector = `[${ClickOutsideChromeAttribute}]`
```

The owning module holds the listener; other surfaces opt in by tagging
themselves — the modules involved stay unaware of each other. Global runtime
modes are data attributes on `<html>` (`data-size`, `data-color-mode`,
`data-theme`), set once in `app.vue`. Bind optional attributes with
`:data-focused="focused || null"` so false renders as absent, not `"false"`.

## Additional resources

- `references/patterns.md` — module registration, control-plane excerpt, and
  the supporting utilities as a worked example
