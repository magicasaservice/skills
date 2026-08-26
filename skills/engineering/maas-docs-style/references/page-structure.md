# Page structure: canonical skeletons per page type

Four page types cover maas documentation. Pick the skeleton by what the page
documents, then keep every page of that type in the same order. Component
names are illustrative.

## Component / plugin page

For a published package’s component or plugin, documented next to its source.

```
# ComponentName
One-line pitch: what it is + when you’d use it.
<live demo>

## Anatomy
### Simple / ### Composed (copy-pasteable <template> + <script setup> blocks)

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

## One owner per fact

Every fact has exactly one home on the page. Prose sections (usage
guidelines, examples, accessibility) own behaviour, reasoning and the
interactions between features. The API reference owns signatures: names,
types and defaults, with descriptions at label length, one short clause,
two at most. A row with more to say links to the section that says it
rather than retelling it. Frontmatter-driven tables (data attributes, CSS
variables) are the only home for their facts and keep their content. The
per-part reference layout itself (one section per part, its own Props,
Options, Emits and Slot Props tables) always stays.

## Module page

For a self-contained feature module (a provider–composable pair with its own
components, often state-machine driven), documented in a project docs app.
Skip Installation entirely; layer or workspace consumption is documented
once, centrally.

```
# Module Name
One-line pitch: what it is + when you’d use it.
<live demo, when the module renders something>

## Anatomy                 (the real minimal composition, lifted from app usage)

## The Slot Contract       (only for slot-composed modules: which slots exist,
                            which are active when, and why)

## Components              (grouped tables: part → role + notable props/slots/emits;
                            be thorough on structural parts, brief on leaves)

## API Reference
### <Provider>             (#### Props / #### Options)
### use<Module>            (returns table: name → meaning)

## Errors                  (assertion messages match the source one-for-one)
## Caveats                 (cause-first: mechanism, then rule)
```

## Utility reference page

For utility classes or helper APIs. The table carries the prose; the
surrounding text stays minimal.

```
# Utility Group
One-line pitch + link to the concept page that explains the system.
<usage pattern as a fenced code block, when one exists>

## <Group> Utilities       (table: Class → Description; document the naming
                            scheme once instead of enumerating hundreds of rows)
<live demo>

## Exceptions / Caveats    (cause-first)
```

## Concept page

For foundations: the ideas behind a system (color, theming, materials), not
its API. Prose leads; references link out to the matching utility pages.

```
# Concept
Frontmatter description doubles as the standfirst.

Short lede: what the system is, in two or three sentences.
<live demo or overview table>

## <Aspect>                (one H2 per aspect, 1–3 short paragraphs each,
                            linking to the reference pages for detail)

## Usage mapping           (when useful: table of where the system appears,
                            Component → Usage)
```

Rules that make the skeletons work:

- The **Errors** section mirrors the assertion messages and error codes in the
  source exactly; docs and runtime errors are the same contract.
- API tables carry their prose in the description cells; the surrounding text
  stays minimal.
- Examples are real, runnable demo files, not inline pseudo-code; anatomy
  snippets are condensed from real app usage, never invented.
- One page per component. Grouped pages are reserved for modules and compound
  component sets whose parts only exist together; grouping unrelated
  components makes them hard to find.

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
