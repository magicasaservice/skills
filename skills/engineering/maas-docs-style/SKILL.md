---
name: maas-docs-style
description: >
  This skill should be used when writing or editing documentation for
  Magic as a Service™ projects — READMEs, docs sites (VitePress), component or
  plugin documentation, API references, guides, or changelog-adjacent prose.
  Trigger on requests to "document", "write docs", "add a README", "describe
  this component", or to edit any .md file in a maas repo. Covers tone of
  voice, page structure, and documentation principles.
metadata:
  version: "0.1.0"
---

# Magic as a Service™ Documentation Style

Write documentation the way Magic as a Service™ does: direct, opinionated, and
example-first. The reference implementation is the Vue Equipment docs
(vue.equipment).

## Voice

- **Instructions: second person, imperative.** "Import the composable directly
  where you need it." Never "you can import" or "one might import".
- **Opinions: first-person plural, stated as beliefs.** The team says "we" and
  explains reasoning: "We believe this leads to cleaner templates and a better
  developer experience." Opinions are welcome — hedging is not.
- **One idea per sentence.** Paragraphs are 1–3 sentences, then a code block.
  If a paragraph needs a fourth sentence, it is probably two paragraphs or a
  list.
- **Cause-first caveats.** Explain the mechanism, then the rule: "In order for
  the drawer to differentiate when the user scrolls and when the user drags,
  any scrollable containers need their overflow value explicitly set."
- **Personality lives at the edges.** Emoji headings, taglines, and playful
  copy belong in the README and marketing pages only. Reference documentation
  is completely dry: no emoji, no jokes, no exclamation marks.
- Use real typography: always the typographic apostrophe (’) in prose, never
  the typewriter apostrophe (') — "it’s", not "it's" — plus curly quotes and
  sparing em dashes. Straight quotes belong in code only — and that includes
  inline code spans showing literal API values: `'inner'` keeps its straight
  ASCII quotes even mid-sentence.
- **Describe observable effects, never internals.** Public docs never mention
  internal state or implementation names; say what an option or event does,
  what it ignores, and its default — the design rationale belongs in a commit
  message, not the option table.

## Principles

- **Show, don’t tell.** Every claim is followed by a copy-pasteable example.
  A section body is often a single sentence plus a fenced code block. Lead
  component docs with a live demo before any explanation.
- **Open with what + when.** The first line after a page title says what the
  thing is and when to reach for it: "MagicDrawer is a flexible, touch
  enabled, unstyled drawer component. Useful for shopping carts, menus, as a
  modal replacement on mobile devices and the like."
- **Docs live next to source.** A component’s documentation is co-located with
  its code (`index.md` beside `index.ts`) so it evolves in the same PR.
- **Identical skeleton per page type.** Every plugin/component doc follows the
  same section order so readers build muscle memory. See
  `references/page-structure.md` for the canonical skeleton.
- **Never repeat shared prose.** Installation boilerplate and other recurring
  sections are included from shared snippet files, not copied.
- **Package-manager tabs, always.** Install commands appear as a tabbed code
  group in this order: pnpm, npm, yarn, bun.

## Additional resources

- `references/page-structure.md` — the canonical page skeleton for component
  and plugin docs, with verbatim voice examples
