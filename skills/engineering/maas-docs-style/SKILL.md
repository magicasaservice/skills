---
name: maas-docs-style
description: >
  This skill should be used when writing or editing documentation for
  Magic as a Service™ projects: READMEs, docs sites (VitePress, Nuxt
  Content), component or plugin documentation, API references, guides, or
  changelog-adjacent prose. Trigger on requests to "document", "write docs",
  "add a README", "describe this component", or to edit any .md file in a
  maas repo. Covers tone of voice, page structure, frontmatter, and
  documentation principles.
metadata:
  version: "1.1.0"
---

# Magic as a Service™ Documentation Style

Write documentation the way Magic as a Service™ does: direct, opinionated, and
example-first. Example names below are illustrative.

## Voice

- **Instructions: second person, imperative.** "Import the composable directly
  where you need it." Never "you can import" or "one might import".
- **Opinions: first-person plural, stated as beliefs.** The team says "we" and
  explains reasoning: "We believe this leads to cleaner templates and a better
  developer experience." Opinions are welcome; hedging is not.
- **One idea per sentence.** Paragraphs are 1–3 sentences, then a code block.
  If a paragraph needs a fourth sentence, it is probably two paragraphs or a
  list.
- **Cause-first caveats.** Explain the mechanism, then the rule: "In order for
  the drawer to differentiate when the user scrolls and when the user drags,
  any scrollable containers need their overflow value explicitly set."
- **No AI-speak in reference prose.** The bans from `maas-writing-style`
  apply to documentation too: no contrastive negation ("it's not X — it's
  Y"; say what the thing is), no hype vocabulary, no throat-clearing, no
  exclamation marks.
- **Personality lives at the edges.** Emoji headings, taglines, and playful
  copy belong in the README and marketing pages only. Reference documentation
  is completely dry: no emoji, no jokes, no exclamation marks.
- Use real typography: always the typographic apostrophe (’) in prose, never
  the typewriter apostrophe ('). Write "it’s", not "it's", and use curly
  quotation marks. Straight quotes belong in code only, and that includes
  inline code spans showing literal API values: `'inner'` keeps its straight
  ASCII quotes even mid-sentence.
- **Em dashes are rare.** Reference prose defaults to commas, colons, and
  short sentences; most paragraphs contain no em dash at all. Never more
  than one dash aside per paragraph, and never a dash where a comma would
  do.
- **Follow the project’s naming canon and spelling.** Each project defines
  its product-name spellings (and US or British English) in its CLAUDE.md;
  apply them exactly, and grep for banned variants before finishing docs
  work. Absent a project canon, maas prose leans British per
  `maas-writing-style`.
- **Describe observable effects, never internals.** Public docs never mention
  internal state or implementation names; say what an option or event does,
  what it ignores, and its default. The design rationale belongs in a commit
  message, not the option table.

## Frontmatter

Docs pages carry frontmatter, and rot starts there. The contract:

- Key order `title` → `description` → `tagline` (or the project’s equivalent).
- Every `description` is non-empty, unique, and informative, never "The X
  component."
- No working notes, SEO drafts, or placeholder values in frontmatter; delete
  what is not rendered or true.

## Principles

- **Show, don’t tell.** Every claim is followed by a copy-pasteable example.
  A section body is often a single sentence plus a fenced code block. Lead
  component docs with a live demo before any explanation.
- **Demos are contracts.** Live demos and previews compile against the
  current API. When an API changes, the demos change in the same session;
  a silently broken demo is worse than none.
- **Open with what + when.** The first line after a page title says what the
  thing is and when to reach for it: "SideSheet is a flexible, touch enabled,
  unstyled sheet component. Useful for shopping carts, menus, as a modal
  replacement on mobile devices and the like."
- **Docs live with the code they describe.** For a published package, the
  component’s documentation is co-located with its code (`index.md` beside
  `index.ts`) so it evolves in the same PR. For a project with a docs app
  (Nuxt Content, VitePress), content lives in the docs tree instead; then
  the same discipline applies as a rule: any change to a documented surface
  updates the matching page in the same session.
- **Identical skeleton per page type.** Every page of a given type follows
  the same section order so readers build muscle memory. See
  `references/page-structure.md` for the canonical skeletons: component or
  plugin, module, utility reference, and concept pages.
- **Never repeat shared prose.** Installation boilerplate and other recurring
  sections are included from shared snippet files, not copied.
- **Package-manager tabs where installation applies.** Install commands
  appear as a tabbed code group in this order: pnpm, npm, yarn, bun. Skip
  installation sections entirely for monorepo-internal packages that arrive
  via workspace or layer; document the consumption mechanism once, centrally.

## Additional resources

- `references/page-structure.md`: the canonical page skeletons per page
  type, with verbatim voice examples
