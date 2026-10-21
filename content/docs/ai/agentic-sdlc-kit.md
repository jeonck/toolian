---
weight: 51950
title: "Agentic SDLC Kit"
description: "Twelve Agent Skills that carry a project from an interview to a verified, deployed system — with stable IDs, a shared traceability matrix, and tests an independent auditor re-runs cold."
icon: "account_tree"
date: "2026-10-17"
lastmod: "2026-10-17"
draft: false
---

[Agency Agents](/docs/ai/agency-agents/) hands an agent specialist roles. [Agentic SDLC
Kit](https://github.com/zeeshanhanif/agentic-sdlc-kit) hands it a **process** —
twelve [Agent Skills](https://agentskills.io) that carry a project from "I have an idea"
through requirements, architecture, UX, a plan, a running repo, a deployment, and a
per-feature build loop that runs for the life of the project. Apache-2.0.

The problem it names plainly: a coding agent left to itself improvises — it redesigns
mid-build, quietly weakens a failing test until it passes, loses its place between
sessions, and ships whatever satisfies the last message in the chat rather than the
requirement. This kit answers with twelve stages that each own exactly one job, and a
shared ID vocabulary so nothing gets re-decided downstream.

## What makes it a kit, not twelve prompts

A requirement minted as `FR-AUTH-007` in the requirements doc is the same ID the
architecture cites, the plan schedules into `FEAT-004`, the feature design turns into
acceptance criteria, implementation satisfies, and an independent auditor signs off in a
shared traceability matrix. No skill invents a reference it can't resolve.

```text
requirements-engineering → software-architecture → ux-foundations
  → implementation-planning → project-scaffolding → (initial-deployment, any time)

per feature, for the life of the project:
  detailed-design → ui-design → feature-implementation → acceptance-verification → next

driving and checking:
  sdlc-orchestrator  — drives the loop, routes change requests and bugs
  pipeline-verify    — read-only audit of every seam between documents, any time
```

| Stage | Produces |
|---|---|
| requirements-engineering | `srs.md`, `use-cases.md`, `rtm.md` |
| software-architecture | `architecture.md` + ADRs, **and the testing decisions everything downstream obeys** |
| ux-foundations | `ux-foundations.md`, `design.md`, `tokens.json` |
| implementation-planning | `implementation-plan.md` — vertical slices as `FEAT` IDs |
| project-scaffolding | a running repo, CI, `AGENTS.md` |
| initial-deployment | a live system — runs once, whenever you choose |
| detailed-design → ui-design → feature-implementation → acceptance-verification | one feature, start to verified |

## Install

Three paths, pick by what you want:

```bash
# everything, works in Claude Code, Cursor, Copilot and others the skills CLI supports
npx skills add https://github.com/zeeshanhanif/agentic-sdlc-kit --skill '*'

# an interactive checklist of all twelve instead of installing silently
npx skills add https://github.com/zeeshanhanif/agentic-sdlc-kit

# just one
npx skills add https://github.com/zeeshanhanif/agentic-sdlc-kit --skill requirements-engineering
```

Claude Code users can take all twelve as one plugin instead:

```text
/plugin marketplace add zeeshanhanif/agentic-sdlc-kit
/plugin install agentic-sdlc-kit@zeeshanhanif
```

The plugin installs as a unit on purpose — the twelve skills share an ID vocabulary and
a traceability matrix with per-column ownership, and a partial plugin install would leave
dangling references. If you want a subset, use the `skills` CLI instead; it's the one
path that installs less than everything.

## Running it

Every stage fires either because you describe the matching intent or because you invoke
it by name:

```text
I'm kicking off a new project — help me gather and write up the requirements.
The requirements are in docs/srs.md — now design the architecture.
Architecture and UX foundations are done — turn them into a build plan.
The plan's ready — scaffold the repo and stand up the walking skeleton.
```

Then, once per feature:

```text
Run the loop until it hits something that needs me.
```

The orchestrator computes the position, invokes the right stage, routes rework back to
implementation, and pauses on anything that needs a human decision — announcing *what*
stopped, *where*, *who* owns the next move, and *how to resume*, every time.

## The contracts worth knowing before you use it

These explain the behaviour you'll actually see, not just the feature list:

**Position is computed, never stored.** No skill keeps a pointer to "where we are" — a
feature folder with `technical-design.md` is *designed*; add `ui-design.md` and it's
*ui-designed*; a fully-checked `tasks.md` is *developer-done*. A brand-new session, or a
fresh context window mid-feature, lands in exactly the right place by reading the repo.

**IDs are immutable and never reused.** Removals are tombstoned (row kept, marked
`Removed`), never deleted or renumbered, so every downstream reference stays resolvable.

**The testing chain is decided once, at the top.** `software-architecture` picks the
frameworks and the coverage stance — *none* / *report-only* / *enforced threshold N%*.
Scaffolding realizes exactly that; implementation inherits it; and the **anti-fake-green
rule** makes the coverage config itself off-limits — lowering a threshold is treated the
same as deleting a failing assertion.

**Escalate, don't fork.** A feature needing a new entity, a screen needing an
off-system colour, implementation hitting a contract change — all of it stops and
escalates to the owning document's amendment path. Nothing gets quietly redesigned two
layers down from where the decision actually lives.

## The part that makes the "verified" claim mean something

`feature-implementation` builds and marks itself developer-done — then
`acceptance-verification` audits it as a **separate skill that never fixes production
code**. Three rules make that separation real rather than cosmetic:

- Every check is **re-derived from the authoritative documents** — acceptance criteria
  cross-checked against the verbatim requirement text — never from `tasks.md` checkboxes
  or a session's claimed greens. *Claims are exhibits; observations are evidence.*
- Every suite is **re-run fresh from a cold local stack**, with an independent diff
  review of the feature's commit range for loosened assertions, new skips, or
  coverage-config drift that quietly admitted this one feature.
- The verdict is **accepted / rework / design defect** — never partial. Rework routes
  back to implementation; a design-level problem routes to `detailed-design`'s amendment
  path instead of getting patched where it was found.

If you've used [DeepEval](/docs/ai/deepeval/) or [promptfoo](/docs/ai/promptfoo/) to stop
trusting an agent's self-report on model output, this is the same instinct applied to the
build itself: the thing that built it is never the only thing that gets to say it's done.

## You don't need all twelve

The pipeline right-sizes — a CRUD tool gets a tight architecture document, not a full
arc42 treatment — and every skill degrades gracefully when its upstream documents are
missing. `software-architecture` with no SRS just interviews and states its drivers in
prose instead of citing IDs. A reasonable floor for a small project:
**requirements → architecture → plan → scaffolding**, then the loop. Add
`ux-foundations`/`ui-design` once there's meaningful UI, `initial-deployment` once it
needs to be reachable, `sdlc-orchestrator` once driving the loop by hand gets old, and
`pipeline-verify` once there's enough traceability worth auditing.

You can also start mid-pipeline against an existing codebase, or run only
`requirements-engineering` and stop — nothing forces the whole chain.

## Next

The same CLI, pointed at finding you a job instead of building software →
[career-ops](/docs/ai/career-ops/)
