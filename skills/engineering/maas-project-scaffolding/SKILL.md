---
name: maas-project-scaffolding
description: >
  This skill should be used when creating or setting up a new Magic as a
  Service™ project or repository, adding an app or package to a monorepo, or
  writing or
  reviewing a package.json. Trigger on requests to "start a new project",
  "scaffold", "set up a repo/monorepo", "add a package", or "create a
  package.json". Covers the turborepo-starter template and package.json
  conventions.
metadata:
  version: "0.1.0"
---

# Magic as a Service™ Project Scaffolding

New projects start from the template, not from scratch:
https://github.com/magicasaservice/turborepo-starter

The template is the source of truth for the starting setup — pnpm workspaces,
Turborepo tasks, `@maas/config` wiring. Never recreate its files by hand or
from memory; use the repository as a template (or clone it) and read the
current files from there.

## Starting a project

1. Use `magicasaservice/turborepo-starter` as the repository template.
2. Delete the `.github` folder — it contains the starter’s dependabot
   auto-update workflows, which must not run in downstream projects.
3. Update the README to reflect the project’s actual structure.

## package.json conventions

These rules apply beyond the starter — to every package.json written or
reviewed in a maas repo:

- **Name**: scoped, `@maas/<project>`.
- **`"private": true`** unless the package is actually published to npm.
- **`contributors`**, not `author`: an array of `{ "name", "url" }` entries
  pointing at GitHub profiles.
- **ESM only**: `"type": "module"` in publishable packages.
- **Scripts delegate to Turborepo**: root `build`, `dev`, and `lint` are
  `turbo run <task>` — per-package behavior lives in the packages, never in
  root scripts.
- **Engines pinned twice**: an `engines` field plus an exact `packageManager`
  entry, so pnpm versions never drift across the team.
- **`@maas/config` is always a root devDependency** — it supplies Prettier,
  ESLint, and TypeScript presets (see the `maas-conventions` skill for the
  wiring).

Internal workspace packages additionally stay unbuilt and side-effect free —
the consuming app compiles them:

```json
{
  "name": "@maas/example-package",
  "private": true,
  "sideEffects": false,
  "exports": "./index.ts"
}
```
