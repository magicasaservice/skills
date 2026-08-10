---
name: maas-conventions
description: >
  This skill should be used whenever writing, generating, editing, refactoring,
  or reviewing code for Magic as a Service™ projects, especially TypeScript,
  JavaScript, Vue, or Nuxt. Trigger on requests to "write", "create", "add",
  "refactor", or "review" code, or to change any source file in a maas repo.
  Covers Prettier formatting, ESLint setup, TypeScript conventions, comment
  style, and Git commit rules.
metadata:
  version: "1.1.0"
---

# Magic as a Service™ Conventions

Apply these conventions to all code produced or modified for Magic as a
Service™ projects. If a repo-level config or `CLAUDE.md` contradicts anything here, the
repo-level config wins; this skill sets the org-wide default, not a hard rule.

## Default stack

TypeScript, Node.js, Nuxt, Vue, Tailwind CSS. Assume this stack unless the repo
indicates otherwise.

## Formatting (Prettier)

Every project uses a shared Prettier preset from `@maas/config`. Produce code
that already matches it; do not emit code that a formatting pass would
rewrite:

- No semicolons (`semi: false`)
- Single quotes (`singleQuote: true`)
- 2-space indentation (`tabWidth: 2`)
- Trailing commas only where valid in ES5 (`trailingComma: 'es5'`)

How to wire the preset into a repo (dev dependency + `prettier.config.mjs`,
with and without Tailwind) is in `references/formatting.md`.

## Linting (ESLint)

ESLint config also comes from `@maas/config`: an `eslint.config.mjs` at the
repo root (or per application/package in a monorepo) re-exports the `nuxt`,
`vue`, or `typescript` preset, optionally extended. Setup and extension
examples: `references/formatting.md`.

## Separation of concerns

Everything (a function, composable, component, module, or package) owns
exactly one clearly scoped concern, and concerns never cross-pollute:

- **One concern per unit.** If describing what a unit does requires "and", it
  is two units. Split by concern, not by size.
- **Communicate through narrow seams.** Units interact through explicit
  interfaces: arguments, return values, events, shared state objects. Never
  reach into another unit’s internals or duplicate its logic.
- **Keep knowledge in one place.** A rule, default, or piece of state has one
  owner; everyone else asks the owner instead of keeping a copy.
- **Dependencies point one way.** Shared code never imports from its
  consumers. If two units need each other, extract the shared part into a
  third.

This applies at every layer, not just the frontend: an endpoint handler
authenticates and delegates, it does not format; a data transformer owns
shape conversion, it does not fetch; a mail template renders, it does not
decide who receives it. API design, CMS collections, CLI scripts, and
components all answer the same question at every boundary: who owns this?

See the `maas-component-architecture` skill for how this idea plays out in
component code.

## Boundaries

External data never enters the application in its raw shape:

- **Transform at the edge.** API responses, CMS documents, and third-party
  payloads pass through one transformer that produces a shape the
  application owns. Downstream code never handles the external format, so
  external changes stay contained in one file.
- **Null-coalesce at the boundary.** The transformer resolves every optional
  to a safe default. Nothing downstream deals with `undefined`.
- **Derive the type from the transformation.**
  `type Page = ReturnType<typeof pageTransformer>`; the shape is written
  once, in code, and the type follows it.
- **Read configuration in one place.** Secrets and environment variables are
  resolved in a single config module (or the framework’s runtime config) and
  passed on. `process.env` never appears in handlers or business logic.

## Functions

- **One thing per function, no hidden side effects.** A function either
  computes and returns a value, or performs the single effect its name
  states. It never does both, and never touches anything the name doesn’t
  announce (mutating arguments, writing shared state).
- **Parameters are an object unless there is a single value.** One value
  stays positional (`formatPrice(cents)`); anything more takes a payload
  object, so call sites read as named arguments and parameters can be added
  without reordering: `createOrder({ customer, items, discount })`.
- **Prefer `switch` over long `if`/`else` chains** when branching on one
  value. Guard clauses handle the exceptional cases first; a `switch` handles
  the enumeration.

Examples: `references/typescript.md`.

## Comments

Write as few comments as possible; a comment has to earn its place:

- Comment only a non-obvious **why**, never the what. Prefer a
  self-explanatory name over a comment; never write header or section
  comments that restate what the code obviously does.
- Keep the survivors short: one fact, ideally one line.
- No em dashes in comments. A comment that wants a dash aside is carrying
  two facts; keep the one that matters, or use a comma or colon.
- **Never comment CSS**: no `/* … */` in `.css`/`.scss` files or `<style>`
  blocks. If a rule needs justification, it belongs in the commit message.
- In comment **prose**, use typographic apostrophes for contractions and
  possessives: "the panel’s home", "doesn’t". Code inside a comment (string
  literals like `'inner'`, identifiers) keeps straight ASCII quotes.

The `// Public state` / `// Public functions` grouping comments in composables
are the one sanctioned exception to the no-section-comments rule; see the
`maas-vue-conventions` skill.

## TypeScript

- Write **function declarations**, not function expressions, for named
  functions (`function loadUser() {}`, not `const loadUser = () => {}`).
- Use the **`Array<Item>` generic form**, never the `Item[]` bracket form.
- Give every type a **descriptive name**. No single-letter names; this includes
  generic type parameters (`Array<ElementType>`, not `Array<T>`).
- **No `as` type assertions.** Narrow honestly instead: `instanceof` guards,
  type predicates (`(x): x is Thing`), and generic type parameters. An
  assertion hides real type errors. Allowed exceptions: `as const`,
  `Symbol() as InjectionKey<…>`, and genuinely unavoidable library-typing
  workarounds.

Full rationale and before/after examples: `references/typescript.md`.

## Git

- Use **semantic commits** (Conventional Commits): `type(scope): subject`,
  e.g. `feat(auth): add magic-link login`.
- Keep the subject imperative and lowercase; add a body only when the intent
  is not obvious from the diff.
- **Never** reference AI, Claude, Anthropic, "generated by", or any co-author
  attribution in commit messages, PR titles/descriptions, or any other
  Git-related text.

Details and message format: `references/git.md`.

## Delivering code

Code arrives lint-clean. After writing or editing code, run the project’s
lint command (check `package.json` scripts, usually `pnpm lint`) before
reporting the work as done; never leave fixes for the linter to make after
the fact.

## Additional resources

- `references/formatting.md`: wiring `@maas/config` Prettier and ESLint presets into a repo
- `references/typescript.md`: TypeScript conventions with examples
- `references/git.md`: commit message conventions
