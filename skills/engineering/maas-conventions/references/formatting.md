# Formatting & Linting: Prettier & ESLint

All Magic as a Service™ projects get their Prettier and ESLint configuration from
`@maas/config`. Never hand-write Prettier options or ESLint rules in a repo;
pull in a preset, and extend it only when the project genuinely needs it.

## Prettier setup

1. Add `@maas/config` as a dev dependency at the repo root.
2. Create `prettier.config.mjs` at the repo root, pulling in the preset that
   matches the project:

**With Tailwind CSS:**

```js
import { prettierPreset } from '@maas/config/prettier-preset.mjs'

export default prettierPreset
```

**Without Tailwind CSS:**

```js
import { prettierPlainPreset } from '@maas/config/prettier-plain-preset.mjs'

export default prettierPlainPreset
```

## ESLint setup

ESLint presets also live in `@maas/config`. Reference them in an
`eslint.config.mjs` at the repo root, or one per application/package
in a monorepo.

Pick the preset that matches the project:

**Nuxt:**

```js
import { nuxtPreset } from '@maas/config/eslint-nuxt-preset.mjs'

export default nuxtPreset
```

**Vue (without Nuxt):**

```js
import { vuePreset } from '@maas/config/eslint-vue-preset.mjs'

export default vuePreset
```

**TypeScript (no Vue/Nuxt):**

```js
import { typescriptPreset } from '@maas/config/eslint-typescript-preset.mjs'

export default typescriptPreset
```

### Extending a preset

The presets are flat-config arrays, so extend by spreading and appending:

```js
import { typescriptPreset } from '@maas/config/eslint-typescript-preset.mjs'

export default [
  ...typescriptPreset,
  { ignores: ['.nitro', '.output', '.turbo'] },
]
```
