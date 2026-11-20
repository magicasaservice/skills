---
name: maas-release-notes
description: >
  This skill should be used when writing or editing release notes for Magic
  as a Service™ projects: the text of a GitHub release, a changelog entry,
  or any summary of what a version ships. Trigger on requests to "write a
  release note", "draft the release notes", "write the changelog", or "what
  changed in this version". Covers the fixed headings, the length of a
  bullet, what to leave out, and the changelog link.
metadata:
  version: "1.0.0"
---

# Magic as a Service™ Release Notes

Release notes tell the people using a project what a version changes for
them. They are short: a few one-sentence bullets under fixed headings, then
the changelog link. Example names below are invented.

## Format

```markdown
## ⚠️ Breaking Changes

- Sheet requires `motion` 14.5 or later.

## 🍿 Features

- New `useSwipeIntent` composable tells a scroll from a swipe by the direction of a press’s first move.

## ❗️ Bug Fixes

- `SheetTrigger` and `PanelTrigger` fall back to the provider’s `disabled` option when the prop is omitted.

## 📖 Docs

- Sheet has its own page with demos.

**Full Changelog**: https://github.com/<owner>/<repo>/compare/v1.4.0...v1.5.0
```

- Use these four headings, spelled and ordered as above, and leave out any
  that has no entries.
- Write no title, introduction or closing line. Only a milestone such as
  the first stable release opens with a short paragraph.
- End with the compare link from the previous tag to the new one. If the
  version is not tagged yet, say which tag the link assumes.

## Bullets

- **One sentence per bullet.** A first draft runs about twice too long; cut
  it before handing it over.
- **Say what changed for the person using the project.** Describe the
  behaviour they can observe, and put the option, component or function it
  concerns in an inline code span. The cause of a bug and the way it was
  fixed belong in the commit.
- **Add a "before" clause only when the fix is unclear without it.**
  "`SheetTrigger` inside a disabled item is now disabled" needs none.
- **Merge related changes.** Two components that received the same fix are
  one bullet, and so are several steps of one feature.

## What to leave out

- Changes nobody using the project can observe: chores, refactors, tests,
  CI, build tooling and the release commit itself.
- Minor docs and demo tweaks. Docs get a bullet for a new page, a new demo
  or a newly documented feature.
- Fixes to something that never shipped in a release.

## Related

The bans in `maas-writing-style` apply here (No AI-speak, the typographic
apostrophe, no em dash); its sentence length and narrative structure do
not. The Git rule in `maas-conventions` covers release notes too: no AI or
tooling attribution.
