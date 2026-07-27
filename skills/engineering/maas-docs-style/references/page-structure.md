# Page structure — component/plugin docs

The canonical skeleton, in order. Component names are illustrative.

```
# ComponentName
One-line pitch: what it is + when you’d use it.
<live demo>

## Anatomy
### Simple / ### Composed — copy-pasteable <template> + <script setup> blocks

## Installation            (included from a shared snippet)
### Vue / ### Nuxt / ### Direct Import / ### Composable

## Peer Dependencies
### Installation           (tabbed code group: pnpm / npm / yarn / bun)

## API Reference
### <SubComponent>         (one per exported part)
#### Props / #### Options / #### Slots / #### Slot Props / #### CSS Variables

## Events
## Errors                  (error codes match the source assertions one-for-one)
## Caveats
## Examples                (one heading per demo, each with a live demo)
```

Rules that make the skeleton work:

- The **Errors** section mirrors the assertion messages and error codes in the
  source exactly — docs and runtime errors are the same contract.
- API tables carry their prose in the description cells; the surrounding text
  stays minimal.
- Examples are real, runnable demo files, not inline pseudo-code.

## Voice examples

Opening pitch:

> SideSheet is a flexible, touch enabled, unstyled sheet component. Useful
> for things like shopping carts, menus, as a modal replacement on mobile
> devices and the like.

Instruction:

> If you are using Vue, import and add `SideSheetPlugin` to your app.

Opinion:

> Most options are grouped under the `option` prop of the outermost component.
> We believe this leads to cleaner templates and a better developer experience
> as opposed to having multiple props which need to be repeated for each
> nested component.

Cause-first caveat:

> In order for the sheet to differentiate when the user scrolls and when the
> user drags, any scrollable containers within the sheet need to have their
> overflow value explicitly set to `auto` or `scroll`.

Tip (kept short, states the default and the alternative):

> `SideSheetTeleport` is optional. Omit it to keep the sheet mounted at all
> times, with visibility controlled via `v-show`.
