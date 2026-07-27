---
name: maas-writing-style
description: >
  This skill should be used when writing prose for Magic as a Service™ — blog
  posts, articles, announcements, case studies, release notes, or the prose
  parts of documentation. Trigger on requests to "write a post", "draft an
  article", "announce", "write about", or to edit any long-form text for a
  maas project. Covers tone of voice, narrative structure, and the stylistic
  devices that make the writing recognisable.
metadata:
  version: "1.0.0"
---

# Magic as a Service™ Writing Style

Write the way Magic as a Service™ writes on maas.engineering/posts: measured,
narrative, quietly funny, generous with credit. Reference posts:
"Introducing Vue Equipment", "Module Inception", "Decoupling with State
Composables". Verbatim excerpts for every rule: `references/voice-examples.md`.

## Voice

- **"We" by default.** The team speaks as a group. Switch to "I" only for
  personal moments (thanks, opinions clearly held by one author).
- **Understate everything.** Claims are deliberately modest: "pretty solid",
  "fairly straightforward", "we are pretty close", "might be a bit overkill".
  Never "blazingly fast", "game-changing", or any hype vocabulary. Confidence
  comes from the work, not the adjectives.
- **Opinionated but fair.** State preferences plainly, then acknowledge the
  alternative’s merits: "While Pinia definitely has its use cases, it might be
  a bit overkill for simply storing some boolean, string and number values.
  That doesn’t mean it should not be used for more complex state."
- **Dry humor in small doses.** One playful title pun ("Module Inception"), a
  bracketed aside ("15 [and counting] Nuxt modules"), a deadpan understatement
  ("we mostly copy paste some code, change a few variables and names and call
  it a day"). Never more than a few per post; never in the technical core.
- **Credit people by name.** Community members, teammates, and inspirations
  are named and thanked: "Finally I’d like to thank Daniel Roe for always
  being available for a quick and insightful chat."
- **Be honest about detours.** Failed first attempts and later corrections
  stay in the story, even when they undercut it: "we later realised it was
  rather bloated and a bit of a performance bottleneck".
- Address the reader as "you" sparingly, and warmly when you do: "make our
  lives easier in the process, and hopefully by extension, yours too."

## Structure

- **Open with a standfirst.** One or two sentences under the title that
  summarize the whole piece with a hook: "The story about why and how we
  ended up shipping 15 [and counting] Nuxt modules in a single npm package."
- **Tell it as a story.** The default arc is: context → problem → (first
  attempt that didn’t hold up) → solution → conclusion. Ground it in real
  projects, real clients, real dates.
- **Headings are story beats.** Short and plain, often two words: "The
  Problem", "The Solution", "Running into Problems", "Finding a Solution",
  "What’s Next?", "In Conclusion".
- **Length follows depth, not the other way around.** The default post is a
  3-minute read; an in-depth piece can run much longer when the subject earns
  it. The discipline stays identical at any length: paragraphs of 1–4
  sentences, one idea per paragraph, the same narrative arc — a long post is
  more story beats, not longer ones. Cut anything that doesn’t move the story
  or teach the mechanism.
- **Explain cause before rule.** When something must be done a certain way,
  explain the mechanism first, then the consequence — the SSR section pattern:
  what happens on the server, therefore what leaks, therefore the fix.
- **Cite, don’t paraphrase.** Quote documentation and sources verbatim with
  quotation marks and attribution.
- **Close looking forward or giving thanks.** End with what’s next, an
  acknowledgment, or both — never with a summary of what was just said.

## Mechanics

- Rhetorical questions as pivots, used once per post at most: "So what is it?"
- Asides between en or em dashes: "we hit a road block – no straightforward
  way to install Vue plugins within a Nuxt module seemed to exist, which left
  us pondering."
- **Always use the typographic apostrophe (’)** — never the typewriter
  apostrophe (') — in prose, headings, and quotes: "it’s", "we’ve", "Vue’s".
  The same goes for quotation marks (“ ”). Straight quotes belong in code
  only.
- British-leaning spellings ("realised", "favouring"), semicolon-separated
  lists for enumerations of features.
- Introduce every code block with a sentence saying what it shows; the code
  never appears unannounced.

## No AI-speak

The fastest way to break this voice is to fall back on LLM writing tics.
None of the following appear in maas writing:

- **Contrastive negation.** "It’s not just a drawer — it’s a design
  philosophy." "This isn’t about state; it’s about control." Say what the
  thing is, not what it isn’t.
- **Fragment-question drama.** "The problem? State. The solution? Composables."
  "The result? Fewer bugs." Write full sentences; the one allowed rhetorical
  question per post is a transition, not a tic.
- **Em-dash overuse.** One dash-set aside per paragraph at most. If a
  paragraph has two, rewrite one as a sentence or a comma clause. Never use a
  dash where a comma would do.
- **Hype vocabulary.** seamless, robust, powerful, comprehensive, effortless,
  game-changing, supercharge, elevate, unlock, empower, leverage (as a verb),
  delve, dive in/deep dive, unpack, landscape, ecosystem-as-filler,
  "in today’s fast-paced world".
- **Rule-of-three padding.** "Fast, flexible, and powerful." Triads are fine
  when three real things exist; never pad a list to three for rhythm.
- **Throat-clearing.** "It’s worth noting that", "It’s important to
  understand", "Let’s take a closer look". Delete the phrase; keep the point.
- **Summary endings.** "In summary, we’ve seen how…" The reader just read the
  post. Close forward-looking or with thanks (see Structure).
- **Bold-led bullet walls.** Long runs of "**Label.** Explanation" bullets are
  a document pattern, not an article pattern — posts are written in flowing
  paragraphs. (Skills like this one are documents; posts are not.)
- **Symmetric sentence pairs.** "Where X offers speed, Y offers safety."
  Balanced-clause constructions read as generated; vary sentence shapes.

When in doubt: read the sentence aloud. If it sounds like a keynote or a
product page, rewrite it the way you’d explain it to a colleague.

## Related

For the reference-documentation page skeleton (API tables, installation tabs,
anatomy sections), `maas-docs-style` applies on top of this voice.

## Additional resources

- `references/voice-examples.md` — verbatim excerpts from the reference
  posts, organized by device
