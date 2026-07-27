# @maas/skills

Agent skills for Magic as a Service™ — company-wide rules, skills, and references
for Claude Code, Cowork, and any other agent that supports the
[Agent Skills](https://agentskills.io) standard. Open source — external use
welcome under the MIT license.

## Install

Two ways to get the skills, pick per situation:

| Method                  | What you get                                                    |
| ----------------------- | --------------------------------------------------------------- |
| **Plugin marketplace**  | Managed, read-only, updated centrally — the default for the team |
| **`npx skills` (copy)** | Skills copied into your project — editable, forkable, tool-agnostic |

### Plugin marketplace (Claude Code)

```sh
claude plugin marketplace add magicasaservice/skills

claude plugin install engineering-standards@maas
```

Or interactively: run `/plugin` inside Claude Code, pick **Browse
marketplaces**, add this repo, then install `engineering-standards`. For local
testing before pushing, the marketplace source can also be a path:
`claude plugin marketplace add /path/to/skills`.

### Copy into a project (any agent)

The [skills CLI](https://github.com/vercel-labs/skills) installs into Cursor,
Codex, Copilot, and ~70 other agents as well as Claude Code:

```sh
npx skills add magicasaservice/skills
```

### Cowork

Add the marketplace repo in the Cowork desktop app’s plugin settings, then
install `engineering-standards` — or install a packaged `.plugin` file
directly.

## Automatic setup for project repos

To roll the plugin out to everyone working on a maas project, commit this to
the project’s `.claude/settings.json` — Claude Code prompts teammates to
install it when they trust the folder. `includeCoAuthoredBy` enforces our
no-AI-attribution Git rule at the tool level, not just by instruction:

```json
{
  "extraKnownMarketplaces": {
    "maas": {
      "source": {
        "source": "github",
        "repo": "magicasaservice/skills"
      }
    }
  },
  "enabledPlugins": {
    "engineering-standards@maas": true
  },
  "includeCoAuthoredBy": false
}
```

## Skills

| Category      | Skill                         | What it does                                                          |
| ------------- | ----------------------------- | --------------------------------------------------------------------- |
| `engineering` | `maas-conventions`            | Prettier/ESLint via `@maas/config`, TypeScript conventions, Git rules |
| `engineering` | `maas-docs-style`             | Documentation tone of voice and page structure                        |
| `engineering` | `maas-component-architecture` | Design ideas behind Vue Equipment: state composables, emitter, IoC    |
| `engineering` | `maas-project-scaffolding`    | New projects from turborepo-starter; package.json conventions         |
| `engineering` | `maas-docs-first`             | Read a technology’s docs before using it; ask when none are found     |
| `writing`     | `maas-writing-style`          | Tone of voice and narrative structure for articles and blog posts     |

Skills live under `skills/<category>/<skill>/`. The plugin manifest
([.claude-plugin/plugin.json](.claude-plugin/plugin.json)) explicitly lists
which skills ship — folders not on that list (drafts, deprecated skills) stay
in the repo without being distributed.

## Contributing

1. Start a new skill from [template/SKILL.md](template/SKILL.md) under
   `skills/<category>/<skill-name>/`.
2. Add its path to the `skills` array in `.claude-plugin/plugin.json` when it
   is ready to ship, and bump the plugin `version`.
3. Run `npm run validate` (wraps `claude plugin validate .`).
4. Open a PR. Once merged, teammates pick up changes with
   `claude plugin update engineering-standards`.

Authoring guidelines (from the [Agent Skills spec](https://agentskills.io/specification)
and [Anthropic’s best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)):

- `name`: lowercase-hyphen, must match the skill’s directory name.
- `description`: third person, states **what** the skill does and **when** to
  use it, with concrete trigger keywords. This is the only part loaded at
  startup — it decides whether the skill fires.
- Keep `SKILL.md` under ~500 lines; move detail into `references/` files that
  are loaded on demand.
- Skills should be concise: assume the model is smart and only add what it
  doesn’t already know.

## Layout

```
skills/
├── .claude-plugin/
│   ├── marketplace.json           # marketplace manifest (marketplace name: maas)
│   └── plugin.json                # plugin manifest + published-skill allowlist
├── skills/
│   ├── engineering/                # one folder per category
│   │   ├── maas-conventions/
│   │   │   ├── SKILL.md
│   │   │   └── references/
│   │   ├── maas-docs-style/
│   │   ├── maas-component-architecture/
│   │   ├── maas-project-scaffolding/
│   │   └── maas-docs-first/
│   └── writing/
│       └── maas-writing-style/
├── template/
│   └── SKILL.md                   # starting point for new skills
├── LICENSE
└── README.md
```
