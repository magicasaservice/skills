---
name: maas-docs-first
description: >
  This skill should be used whenever working with a library, framework,
  plugin, module, or tool in a Magic as a Service™ project — before installing
  a dependency, using an unfamiliar API, configuring a plugin, or writing
  integration code. Trigger on requests to "install", "add", "integrate",
  "configure", or "use" any third-party or internal technology. Requires
  reading the technology’s documentation before using it.
metadata:
  version: "0.1.0"
---

# Magic as a Service™ Docs First

Never use a technology from memory alone. Training data goes stale; APIs
change between versions. Before writing code against any library, plugin,
framework, or tool — third-party or internal — read its current
documentation.

## The rule

1. **Find the version actually in use.** Check `package.json` and the
   lockfile — then read the docs for that version, not the latest.
2. **Read before writing.** Locate documentation in this order:
   - LLM-specific docs: `llms.txt` / `llms-full.txt` at the project’s docs
     domain (e.g. `https://example.com/llms.txt`), or docs pages the project
     provides specifically for agents.
   - The official documentation site.
   - The package README and repository.
   - As a last resort, the installed source and type definitions in
     `node_modules` — types don’t lie about the current API.
3. **If no documentation can be found, stop and ask.** Ask the user to point
   to the docs instead of guessing from memory. A wrong guess against an
   undocumented API costs more than the question.

## What this applies to

- Adding or updating any dependency.
- Using a plugin or module for the first time in a session — including
  internal ones (`@maas/vue-equipment`, `@maas/config`, Payload plugins).
- Configuring tools (Nuxt modules, Vite/Turborepo config, CI).
- Any API the agent has not verified against docs in the current session.

Reading the docs once per technology per session is enough — the point is
verification before first use, not re-reading before every call.
