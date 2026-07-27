# Blocks and transformers — worked example

Section and field names are illustrative; the shapes are the convention.

## The template

Keep a commented-out scaffold at `src/sections/_template.ts`:

```ts
import type { Block } from 'payload'
import type { XXXSectionSchema } from '../types/auto-generated'

const xxxSection: Block = {
  slug: 'xxx',
  interfaceName: 'XXXSectionSchema',
  fields: [],
}

function xxxSectionTransformer(section: XXXSectionSchema) {
  return {
    id: section.id ?? '',
    blockType: section.blockType,
    data: {},
    layout: {},
  }
}

type XXXSection = ReturnType<typeof xxxSectionTransformer>

export { xxxSection, xxxSectionTransformer, type XXXSection }
```

## A worked section

`src/sections/teasers/teaserGrid.ts` — fields from the shared library, text
passed through the house text utilities, presentation split into `layout`:

```ts
import { itemsField } from '@/fields/item'
import { minimalContainerField } from '@/fields/container'
import { calloutField } from '@/fields/callout'
import { headlineField } from '@/fields/headline'

import { itemsTransformer } from '../../transformers/fields/itemTransformer'
import { containerTransformer } from '../../transformers/fields/containerTransformer'

import { convertNewline } from '../../utilities/convertNewline'
import { sanitizeText } from '../../utilities/sanitizeText'

import type { Block } from 'payload'
import type { TeaserGridSectionSchema } from '../../types/auto-generated'

const teaserGridSection: Block = {
  slug: 'teaser-grid',
  interfaceName: 'TeaserGridSectionSchema',
  fields: [calloutField, headlineField, itemsField, minimalContainerField],
}

function teaserGridSectionTransformer(section: TeaserGridSectionSchema) {
  return {
    id: section.id ?? '',
    blockType: section.blockType,
    data: {
      callout: sanitizeText(section.callout ?? ''),
      headline: convertNewline(section.headline ?? ''),
      items: itemsTransformer(section.items),
    },
    layout: {
      container: containerTransformer(section.container),
    },
  }
}

type TeaserGridSection = ReturnType<typeof teaserGridSectionTransformer>

export { teaserGridSection, teaserGridSectionTransformer, type TeaserGridSection }
```

## Registration and dispatch

A new section is wired in three places:

1. **Block list** — add it to the curated arrays in `src/fields/sections.ts`
   (full / singles / typeface lists reuse the same section blocks).
2. **Dispatch** — add a case to
   `src/transformers/fields/sectionTransformer.ts`, the single `switch`
   mapping every `blockType` slug to its transformer.
3. **Frontend component** — create the matching
   `<PascalCase>Section.vue`; the renderer resolves it by convention:

```ts
const SectionComponent = resolveComponent(
  `${pascalCase(section.blockType)}Section`
)
// <section-component :id :data="section.data" :layout="section.layout" />
```

## Field factory shapes

Const with spread-override (default style):

```ts
{ ...nameField, required: true, admin: { position: 'sidebar' } }
```

`deepmerge` factory (only when arguments are needed):

```ts
export function slugField(
  fieldToUse = 'title',
  overrides: Partial<TextField> = {}
): TextField {
  const defaults: TextField = {
    name: 'slug',
    type: 'text',
    index: true,
    required: true,
    hooks: { beforeValidate: [formatSlug(fieldToUse)] },
    admin: { position: 'sidebar' },
  }
  return deepmerge({ all: true })(defaults, overrides) as TextField
}
```

## Cache hooks

```ts
const { afterChange, afterDelete } = createCacheHooks('pages')

// in the collection config:
hooks: { afterChange: [afterChange], afterDelete: [afterDelete] }
```

The factory is draft-aware — it only revalidates when the published version
is affected. Published reads go through `createCachedQuery(fn, keyParts,
tags)` so tags line up with the hooks.
