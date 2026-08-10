---
name: maas-payload
description: >
  This skill should be used when working on a Magic as a Service™ Payload CMS
  backend or a frontend consuming it: creating collections, blocks, sections,
  fields, transformers, endpoints, or hooks, or fetching CMS content from a
  Nuxt app. Trigger on requests to "add a collection/block/section/field",
  "expose content to the frontend", "add an endpoint", or any work in a
  payload.config.ts, src/sections, src/fields, or src/transformers directory.
  Covers the block+transformer contract, field factories, and the typed
  backend-to-frontend bridge.
metadata:
  version: "1.1.0"
---

# Magic as a Service™ Payload

How we build Payload backends. The governing idea: **the frontend never sees
a Payload document.** Every piece of content passes through a transformer
that produces a stable, null-safe, frontend-owned shape. Example names are
illustrative.

## Blocks and sections: the three-export contract

Every section/block lives in one file exporting exactly three things: the
block config, its transformer, and the transformer’s return type.

- The block config sets `interfaceName` (drives the generated type name) and
  composes fields from the shared field library, with no inline field
  definitions.
- The transformer takes the generated schema type and returns
  `{ id, blockType, data, layout }`: content in `data`, presentation
  concerns in `layout`, every value null-coalesced to a safe default.
- The return type is always `ReturnType<typeof xxxTransformer>`, never
  hand-written.

Start new sections from `src/sections/_template.ts`. Register the block in
the curated lists in `src/fields/sections.ts` and add its case to the
dispatch `switch` in `src/transformers/fields/sectionTransformer.ts`.
Full example: `references/blocks.md`.

## The field library

Fields are shared, one per file, under `src/fields/`:

- **Named variants over parameters**: `containerField`,
  `minimalContainerField`, `proseContainerField`, not one field with
  options. Call-site tweaks use spread-override:
  `{ ...nameField, required: true }`.
- Factory functions (with `deepmerge` over a defaults object) only when a
  field genuinely needs arguments: `slugField('title')`.
- Composites: `src/groups/` bundles related fields
  (`mediaGroup` = square/landscape/portrait uploads); `src/tabs/` ships whole
  admin tabs (`seoTab`, `previewTab`) dropped into collections wholesale.
- The preset pattern: a `presets` collection holds reusable content; other
  documents relate to it plus an `overrideField`; transformers merge with a
  custom `defu` where overrides win and empty strings never override.

## Collections

- One collection per file under `src/collections/`; listed in
  `payload.config.ts` grouped by admin `group` with comment banners.
- Small collection = single file with inline endpoint handlers; large
  collection = directory with `index.ts`, `endpoints/`, `fields/`.
- Hooks are formulaic: `createCacheHooks(tag)` for draft-aware cache
  invalidation on ~every public collection; field-level `beforeValidate`
  slugify; field-level derived values. Nothing else without good reason.

## The typed frontend bridge (no codegen)

- The backend package exports a pure type barrel (`src/index.ts`,
  `export type *` of all transformer return types). Frontends
  `import type { PageTransformerReturn } from 'cms-backend'`: end-to-end
  types with no SDK and no extra codegen.
- Content is served by hand-written custom endpoints returning transformer
  output, uniform across collections:
  `GET /api/{collection}/custom/{slug}?tenant={tenant}&draft={bool}`, with
  SEO always a separate `/custom/seo/{slug}` route (fetched with `useFetch`
  so it is available for SSR meta; content uses `useLazyFetch`).
- `draft === 'true'` switches to a live query with
  `_status: { in: 'draft,published' }`; published requests go through the
  cached query wrapper.

## Rendering on the Nuxt side

Sections resolve to components by convention
(`resolveComponent(pascalCase(section.blockType) + 'Section')`), and every
section component accepts exactly two props: `data` and `layout`. Vertical
rhythm between sections is decided centrally in the area renderer, never per
component.

## House opinions

- Richtext ships as a transformed block array (via converter pipeline), not
  raw Lexical JSON; two editor configs exist (`editorConfig`,
  `reducedEditorConfig`); pick the reduced one for constrained fields.
- Media behavior (S3, Mux, uploads) comes from
  `@maas/payload-plugin-media-cloud`; the media collection itself stays
  minimal.
- MCP plugin, when enabled, is hardened: allowlisted collections, find +
  update only, gated behind an env flag.

## Additional resources

- `references/blocks.md`: the template, a real section, and the dispatch
  pattern with real code
