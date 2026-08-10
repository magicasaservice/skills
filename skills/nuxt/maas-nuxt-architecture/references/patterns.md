# Patterns: worked example

Names below (`OmniBar`, `ChatDock`, `app-base`) are illustrative; the shapes
are the convention.

## Feature module registration

The module’s `index.ts` declares the whole public surface:

```ts
export default defineNuxtModule({
  meta: { name: 'OmniBar' },
  setup() {
    const resolver = createResolver(import.meta.url)
    addComponentsDir({
      path: resolver.resolve('./components'),
      pathPrefix: false,
    })
    addImports([
      { from: resolver.resolve('./composables/useOmniBar'), name: 'useOmniBar' },
    ])
  },
})
```

Registered by path in the layer’s `nuxt.config.ts`:

```ts
modules: [
  'app-base/app/modules/OmniBar',
  'app-base/app/modules/ChatDock',
]
```

## Control-plane excerpt

One composable, called once from `app.vue`, owns all cross-module reactions:

```ts
export function useControlPlane() {
  const omniBarApi = useOmniBar(OmniBarIds.App)
  const chatDockApi = useChatDock(ChatDockIds.App)
  const mainMenuApi = useCommandMenu(CommandIds.MainMenu)
  const settingsDrawerApi = useDrawer(DrawerIds.Settings)

  function enterCallback(payload: AppEmitterEvents['enter']) {
    switch (payload) {
      case CommandIds.MainMenu:
        settingsDrawerApi.close()
        omniBarApi.rootActor.value.send({ type: 'size.save' })
        omniBarApi.rootActor.value.send({ type: 'max' })
        break
    }
  }

  emitter.on('enter', enterCallback)

  onBeforeUnmount(() => {
    emitter.off('enter', enterCallback)
  })
}
```

Route-driven reconciliation follows the same shape: a `syncChrome()`
function maps `route.name` to a burst of machine events, so page components
carry no chrome logic.

## ID enums

```ts
export enum ToastIds {
  Notifications = 'toast--notifications',
  ChatDock = 'toast--chat-dock',
}

export enum DrawerIds {
  Settings = 'drawer--settings',
  Sidebar = 'drawer--sidebar',
}
```

## Data-attribute contract in use

```ts
export const MainMenuDismissAttribute = 'data-main-menu-dismiss'
export const MainMenuPersistAttribute = 'data-main-menu-persist'
```

The menu owns the pointerdown listener; the app layer tags the surfaces (the
omni bar dismisses, its chat dock persists). Consumption side:

```ts
onClickOutside(container, callback, {
  ignore: [ClickOutsideChromeSelector],
})
```

## Supporting utilities worth reusing

- `createContext<T>('ProviderName')`: Radix-style tuple
  `[injectX, provideX]` that throws a formatted error naming the required
  provider.
- `nextFrame()`: promisified `requestAnimationFrame` for sequencing
  layout-dependent work.
- Chrome bounds via `useState` keys: measuring components publish
  (`useElementBounding` + `watchEffect`), content reads. No refs passed
  around.
