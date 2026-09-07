---
weight: 5085
title: "Frontend Design Skills"
description: "Five skills that give an agent taste — aesthetic direction, a UI audit, motion fixes, and a design-decision library."
icon: "palette"
date: "2026-09-07"
lastmod: "2026-09-07"
draft: false
---

Agents write CSS that works and looks like every other page an agent has written:
a gradient hero, a big number with a small label, `ease-in-out` on everything, shadows
where a border belongs. The code isn't wrong, it's just anonymous. These five skills are
the accumulated taste of people who do this professionally, written down where an agent
will actually read it.

Unlike the [subagent library](/docs/ai/agency-agents/), these aren't roles — they're
knowledge the agent loads when the task turns visual.

| Skill | Job | From |
|---|---|---|
| `frontend-design` | Aesthetic direction for new UI | [anthropics/skills](https://github.com/anthropics/skills) |
| `improve-ui` | Find what's wrong with UI you already have | [ibelick/ui-skills](https://github.com/ibelick/ui-skills) |
| `improve-animations` | Audit and fix motion | [emilkowalski/skills](https://github.com/emilkowalski/skills) |
| `ui-ux-pro-max` | A searchable library of style, colour, and UX decisions | [nextlevelbuilder](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) |
| `web-design-guidelines` | Review UI against accessibility and UX rules | [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills) |

## Installing

Most of these ship through `skills.sh`, which installs into whatever agent you run:

```bash
npx skills add vercel-labs/agent-skills      # web-design-guidelines, react-best-practices
npx skills@latest add emilkowalski/skills    # improve-animations and eleven others
npx ui-skills start                          # browse ibelick's registry interactively
```

`ui-ux-pro-max` comes as a Claude Code plugin:

```
/plugin marketplace add nextlevelbuilder/ui-ux-pro-max-skill
/plugin install ui-ux-pro-max@ui-ux-pro-max-skill
```

Install the two or three that match what you build. Loading all five means five sets of
opinions competing for the same decision.

## frontend-design — making it not look generated

Anthropic's own skill, and the one with a point of view. It tells the agent to work like
a design lead whose client has already rejected the templated proposals: pick typefaces
deliberately rather than reaching for the defaults, treat the hero as the most
characteristic thing in the subject's world, set a real type scale, keep line length
under 80 characters, and use type as an active part of the design rather than a delivery
vehicle.

It also lists the tells — the default treatments that mark a page as generated — which is
the part that changes output most.

## improve-ui — for UI that already exists

From [ibelick](https://github.com/ibelick/ui-skills)'s registry of design-engineer
skills (MIT, and actively maintained). Where `frontend-design` starts from nothing,
`improve-ui` looks at what you have and says what's weak. Its siblings are worth knowing
too: `baseline-ui`, `fixing-accessibility`, `fixing-motion-performance`, and
`create-design-md`, which writes the design decisions down as a file your agent reads
next time.

```bash
npx ui-skills list --category motion
npx ui-skills get improve-ui
```

There's an MCP endpoint at `https://www.ui-skills.com/mcp` with `list_skills` and
`get_skill`, if you'd rather your agent fetch them on demand.

## improve-animations — motion, from someone who ships it

Emil Kowalski built Sonner and Vaul and worked on interfaces at Vercel and Linear; this
is that experience as a rulebook. The specific complaint it fixes: agents pick `ease-in`
for an enter animation when it should be `ease-out`, use one duration for every
transition, and animate `width` instead of `transform`.

`improve-animations` audits every animation in the codebase and returns **prioritised,
self-contained plans** rather than edits — you read the list and pick. The same
repository carries `animate` (build one correctly from scratch), `review-animations`,
`find-animation-opportunities` (including what *not* to animate), `animation-vocabulary`
for describing what you want precisely, and `apple-design`.

## ui-ux-pro-max — the reference library

The bulkiest of the five, and structured as searchable data rather than prose: on the
version installed here, 79 UI styles, 192 product palettes with the reasoning behind
them, 74 font pairings, 119 UX guidelines, 105 icons, 17 GSAP presets, 25 chart types,
and 22 tech stacks. (Counts move between releases — check what you actually installed.)

Its rules are ranked by impact, and the top of that list is the useful part: accessibility
first (4.5:1 contrast, keyboard navigation, real focus rings), then touch targets at
44×44px, then performance, then style. It's the one to reach for when the question is
"what should this be?" rather than "what's wrong with this?"

## web-design-guidelines — the audit pass

Vercel's skill, and the most mechanical: 100+ rules across accessibility, focus states,
forms, performance, and UX, run against your code. "Review my UI" or "check
accessibility" triggers it. Pair it with `react-best-practices` from the same repository
if you're on React or Next.js.

## Using them together

| Stage | Skill |
|---|---|
| Deciding what it should look like | `frontend-design`, `ui-ux-pro-max` |
| Building it | `ui-ux-pro-max` for stack-specific patterns |
| Reviewing what came out | `web-design-guidelines`, `improve-ui` |
| Making it feel right | `improve-animations` |

Two honest caveats. **A skill is guidance, not a guarantee** — it shifts the odds toward
better defaults; it doesn't make the agent a designer. And **taste that arrives
pre-packaged is still someone else's taste**: these encode the preferences of specific
people, which is exactly why they're good, and exactly why you should override them when
your project wants something else.

## Next

Those are other people's expertise packaged. Now package your own →
[book-to-skill](/docs/ai/book-to-skill/)
