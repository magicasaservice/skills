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
  version: "1.10.0"
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
- **Relaxed, not clipped.** A sentence with one subordinate clause is the
  norm ("If you are using Vue, import and add the plugin to your app.");
  three short declaratives in a row read as generated. Branch with "If you
  …" conditionals rather than commands.
- **A prose budget.** A component or plugin page carries about ten sentences
  of prose outside code and tables; a section is one or two sentences, then
  the block. Cut before you rephrase.
- **Instructions are exact, not easy-going.** An instructional paragraph
  names the command or option verbatim, says what it does mechanically, and
  states the observable result. Mood phrases ("suits a first look", "once
  you are painting your own colours") and vague gestures ("the tree comes
  into the project") are cut: write "run `mirror init --tokens`: it copies
  the token tree into `./tokens` and rewrites `source` in `mirror.config.ts`
  to read from that directory." See `references/docs-voice.md` for the full
  before/after pair.
- **Cause-first caveats.** Explain the mechanism, then the rule: "In order for
  the drawer to differentiate when the user scrolls and when the user drags,
  any scrollable containers need their overflow value explicitly set."
- **No AI-speak in reference prose.** The bans from `maas-writing-style`
  apply to documentation too: no contrastive negation ("it’s not X — it’s
  Y"; say what the thing is), no hype vocabulary, no throat-clearing, no
  exclamation marks.
- **Four tells that read as generated.** The colon-triad ("X: the A, the B,
  and the C": a headline noun phrase followed by exactly three parallel
  clauses); the verbless self-description (a fragment describing the page's
  own contents instead of telling the reader what to do, often opening with
  "The full" or "A complete"); decorative verbs that commit to nothing
  ("carry", "reshape", "surface", "land": name the real action instead); and
  tuned rhythm over information (clauses of near-identical length whose
  content stays fuzzy). The test is actionability: if the reader cannot act
  on a clause, make it concrete or delete it.
- **No lectern.** A fifth tell is the know-it-all register: aphorisms and
  maxims ("a list wants a ScrollArea"), absolutes ("can never disagree"),
  metaphors repeated until they become the argument, self-congratulating
  asides ("which is the point"), and personified components ("the chip
  publishes no opinion"). State the mechanism and the instruction and stop;
  documentation explains, it does not perform wisdom at the reader.
- **Every page stands alone.** Never lean on what the reader supposedly
  knows from other pages or components: "takes the same options every other
  floating component does" tells a reader who landed here first nothing.
  State the facts in place, even when another page documents the same shape,
  and point at this page’s own tables rather than at another component. A
  comparison may add context ("like every other part") only after the fact
  itself is stated; it never substitutes for it.
- **Name no other libraries.** Public documentation never names other
  component libraries, design systems or influences (Material, Base UI,
  Radix and the like), neither as credit nor as comparison. State the
  behaviour and its reason on its own. A token or utility family that
  happens to share a word (a `material-*` glass family) is not a library
  name. Internal design records are exempt; they reference their targets.
- **Demo code carries no comments.** Example fences and their paired
  preview components are clean code: no comments in the template or the
  script. A constraint the reader genuinely needs (a portal switched off so
  the demo stays in its frame) becomes one plain sentence in the page prose
  next to the demo, and only when no section owns it already.
- **Personality lives at the edges.** Emoji headings, taglines, and playful
  copy belong in the README and marketing pages only. Reference documentation
  is dry in content, not in tone: no emoji, no jokes, no exclamation marks,
  but still a person explaining something to a colleague. See
  `references/docs-voice.md` for verbatim samples and before/after pairs.
- Use real typography: always the typographic apostrophe (’) in prose, never
  the typewriter apostrophe ('). Write "it’s", not "it's", and use curly
  quotation marks. Straight quotes belong in code only, and that includes
  inline code spans showing literal API values: `'inner'` keeps its straight
  ASCII quotes even mid-sentence.
- **Dashes are a last resort, and the em dash is not one at all.** The em
  dash (—) never appears in documentation prose. The en dash (–) is allowed
  only where an aside genuinely cannot be a comma, a colon, or a second
  sentence: at most one per paragraph, and most pages carry none.
  Documentation runs on commas, colons, and short sentences. Grep the file
  for — and – before calling docs work finished.
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
- `references/docs-voice.md`: the docs register, verbatim samples, generated
  vs house before/after pairs, and the tells that survive the bans
