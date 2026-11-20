# Docs voice

Reference documentation uses the same voice as the posts in
`maas-writing-style/references/reference-posts.md`, minus the story. Two
verbatim samples from the Vue Equipment docs, then pairs that turn the
generated register into the house one.

## Samples

The Introduction page (prose only):

> Vue Equipment is a collection of ready-to-use plugins and composables for building modern web applications. It comes unstyled, with a flexible, yet opinionated API. It’s fully typed and can be integrated in both Vue and Nuxt.
>
> **Vision.** Over the years, we have found ourselves facing a common problem: driven by the lack of options in the Vue ecosystem, we needed to implement solutions from the ground-up, over and over again. Vue Equipment is our attempt to solve this.
>
> **Architecture.** Vue Equipment does not aim to be a component library, but instead aims to help reduce the complexities of building web applications.
>
> - Vue Equipment is largely unstyled. Any CSS we ship is crucial for the functionality. Relevant values are available as CSS variables and can be overriden.
> - Most options are grouped under the `option` prop of the outermost component. We believe this leads to cleaner templates and a better developer experience as opposed to having multiple props which need to be repeated for each nested component.
> - Each plugin comes with a composable to interact with the components.
> - We recommend using Enums for Ids to ensure consistency when using Vue Equipment.
>
> **Contribution.** Please don’t hesitate to reach out should you have an idea for another plugin or would like to contribute in some other way. We think that the initial version of Vue Equipment already provides some value, but would like to expand it further in the future.

A plugin page (every sentence of prose it carries, the rest is code and tables):

> MagicDrawer is a flexible, touch enabled, unstyled drawer component. Useful for things like shopping carts, menus, as a modal replacement on mobile devices and the like.
>
> MagicDrawer can be used as a single self-contained component or composed from its individual parts for full control.
>
> `MagicDrawerTeleport` is optional. Omit it to keep the drawer mounted at all times, with visibility controlled via `v-show`.
>
> If you are using Vue, import and add `MagicDrawerPlugin` to your app.
>
> The drawer is available as a Nuxt module. In your Nuxt config file add `@maas/vue-equipment/nuxt` to your modules and add `MagicDrawer` to the plugins in your configuration.
>
> If you prefer a more granular approach, components can be directly imported.
>
> In order to interact with the drawer from anywhere within your app, we provide a `useMagicDrawer` composable. Import it directly when needed.
>
> If you haven’t installed the required peer dependencies automatically, you’ll need to install the following packages manually.

What to copy: one sentence of what and one of when, then the demo; "we" when introducing something we provide or recommend; "If you are using…" conditionals to branch; headings a reader would guess (Vue, Nuxt, Direct Import, Composable); about ten sentences of prose on a whole component page.

## Before and after

Pairs from a real docs pass. The left column is what a model writes when told to be dry; the right is the house voice.

| Generated                                                                                                                                                             | House                                                                                                                                                                                                                                                                                        |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Mirror components render markup and publish state. Everything you see is yours to declare, through four surfaces that never overlap.                                  | Mirror’s components don’t come with a look. They render the markup, keep track of their state and tell you about it, and leave everything visual to you. In practice there are four places where that happens: data attributes, slot props, CSS variables and the generated utility classes. |
| Every state a part can be in is published as a `data-*` attribute. That is what your selectors read, so appearance needs no prop and no state class.                  | Whenever a part changes state, it writes that state to a `data-*` attribute on its element. This is what we style against, both inside Mirror and in our own apps, which is also why none of the components take a `variant` or `color` prop.                                                |
| Boolean state renders the string `true`, or the attribute is absent. Select on the value, never on the bare attribute.                                                | Boolean states are written as the string `true` and removed entirely when false, so selectors should check the value rather than the mere presence of the attribute.                                                                                                                         |
| Skip it and most components look fine while `Slider` renders as overlapping boxes and popups size to their content instead of their trigger.                          | Without it most components still look fine, but the slider thumbs pile up in one corner and popups size to their content rather than their trigger, so import it before anything else.                                                                                                       |
| Each layer stands on its own. Take the tokens without the utilities, take the utilities without the components, or style the components with something else entirely. | You don’t have to use all three. The tokens work on their own, the utilities only need the tokens, and the components can be styled with whatever you already have.                                                                                                                          |
| A border that needs to be opaque is a background behind a hairline, not a border colour.                                                                              | Borders are translucent by design so they work on any background. If you need an opaque edge, put a background behind a hairline instead of looking for an opaque border colour.                                                                                                             |
| A tri-state checkbox for a value a form submits; `Switch` takes effect immediately.                                                                                   | Checkbox is a tri-state checkbox that takes part in forms. Useful wherever a choice should only count once the form is submitted; for settings that apply immediately, use Switch.                                                                                                           |
| **A checkbox needs an accessible name.** Pair it with a `Label` inside a `Field`, or give the root an `aria-label`.                                                   | Make sure the checkbox has an accessible name, either through a `Label` inside a `Field` or an `aria-label` on the root.                                                                                                                                                                     |
| The shape is settled. Parts, namespaces, the `data-*` contract, the slot props and the public CSS variables are the contract v2 commits to.                           | We consider the overall shape settled: the parts, the namespaces, the data attributes and the slot props won’t change before the stable release. Option names and CSS variables might still move a little, and we will list every change in the release notes.                               |
| Reach for the intent groups instead. For a genuine one-off, an arbitrary value is the honest escape hatch.                                                            | If you need a colour that isn’t in the palette, use one of the intent groups first; they are the ones a theme can move. For a real one-off, an arbitrary value is fine.                                                                                                                      |

## The instructional register

Instructional prose is where easy-going writing costs the most: the reader is
mid-task and needs the command, what it does, and what they will see. One
pair, from a real correction:

| Easy-going                                                                                                                                                                                                  | Exact                                                                                                                                                                                                                             |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| The config above reads our tokens over the network on every build, which suits a first look at Mirror. Once you are painting your own colours, pass `--tokens` and the tree comes into the project instead. | The config above downloads the token repository on every build. To edit the tokens, run `mirror init --tokens`: it copies the token tree into `./tokens` and rewrites `source` in `mirror.config.ts` to read from that directory. |

The test: after reading the paragraph, the reader knows the exact command to
type, what it changes on disk, and how the next run behaves. Mood ("suits a
first look") and gesture ("comes into the project") fail that test; a named
file and a named key pass it.

## Caveats and component prose

Pairs from a pass over a plugin page’s component section and caveats,
where the first draft ran twice the length the page needed. The left
column passed the bans and still read as generated: it announced topics,
told the story behind each rule, and let parts act like people.

| Generated                                                                                                                                                                                                                                                                                                                                     | House                                                                                                                                                                                                   |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| The tray handles situations where dragging and scrolling might interfere with each other on touch devices. In order for the tray to differentiate when the user scrolls and when the user drags, any scrollable containers within the tray need to have their overflow value explicitly set to `auto` or `scroll`.                            | In order for the tray to differentiate when the user scrolls and when the user drags, any scrollable containers within the tray need to have their overflow value explicitly set to `auto` or `scroll`. |
| While a handle is being dragged, the tray disables text selection on the whole document and restores it on release. Without this, a pointer that leaves the handle would keep extending a selection across the page. A trigger disables it only once its drag moves past `threshold.lock`, so a long press on its content still selects text. | A handle drag disables text selection on the whole document until release. A trigger does so only past `threshold.lock`, so a long press on its content still selects text.                             |
| If an element inside a trigger runs its own drag, like a swipe action on a list row, stop `pointerdown` from propagating on that element. Otherwise the tray starts a drag from the same press and moves along with it.                                                                                                                       | Stop `pointerdown` from propagating on elements inside a trigger that drag on their own, like a swipe action on a list row, or the same press drags the side too.                                       |
| A press starts the drag right away, so the side follows the pointer from the first pixel.                                                                                                                                                                                                                                                     | The drag starts on press.                                                                                                                                                                               |
| Pass `disabled` as a ref or getter instead of a boolean and the trigger reads it on every move: a trigger disabled during a press lets the press go, and one disabled mid-drag returns its side to rest.                                                                                                                                      | Pass `disabled` as a ref or getter: the trigger reads it on every move, drops the press when disabled before the drag starts and snaps the side back to rest when disabled mid-drag.                    |
| With sides along both axes, the axis the move leans to wins.                                                                                                                                                                                                                                                                                  | With sides on both axes, the axis with the larger move wins.                                                                                                                                            |
| Makes an element a drag surface for one side of the tray, so the user can drag a sidebar or panel shut from anywhere on it.                                                                                                                                                                                                                   | Drags a side of the tray from any element. Wrap a sidebar in it and the sidebar can be dragged shut from anywhere on it.                                                                                |

What the pass removed: the sentence that announces the topic, the harm a
rule prevents once the rule is stated, platform and signal trivia that
change nothing the reader types, and verbs that make a part act ("lets the
press go", "returns its side to rest", "runs its own drag", "moves along
with it"). What it kept: every number, option name, event name and
instruction.

## Tells that survive the bans

These passed the `maas-writing-style` list and still read as generated:

- Teaser headings and value promises. "What breaks quietly", "These are the
  ones worth budgeting time for", "This is where it gets interesting". A
  heading names the thing ("Removed, without an error"); a section starts
  with its content, never with a sentence about how useful the section is.
- Aphorism closers. A short, quotable sentence ending a paragraph ("The input is the typeahead.") once is fine; one per paragraph is a tic.
- Three clipped declaratives in a row. Join two with a subordinate clause.
- The definitional opener repeated on every page ("X is a Y that Z."). Say what it is and when to use it, then move on.
- Library nouns in consumer prose: surface, contract, seam, rung, publish, mint, carry. Consumers read state, attribute, class, value.
- Bold-led guideline bullets. Write the guideline as a sentence.
- Tables where one sentence would do, and hard-coded counts ("fifteen components") that rot.
