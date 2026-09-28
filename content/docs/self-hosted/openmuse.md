---
weight: 160500
title: "OpenMuse"
description: "A self-hosted personal agent with its own browser, Linux terminal, and files — durable tasks you can pause, review, and take over."
icon: "assistant"
date: "2026-09-28"
lastmod: "2026-09-28"
draft: false
---

The assistant products you subscribe to run on someone else's machine, read your mail
there, and show you a chat box. [OpenMuse](https://github.com/CopilotKit/OpenMuse) is
CopilotKit's MIT-licensed template for the same idea on your own hardware: a personal
agent with **a persistent Chromium profile, an optional Linux container, a filesystem,
and work that survives a restart**.

The framing that makes it interesting is the reviewable middle. Ask for an outcome, watch
the plan, approve the steps that touch the outside world, and take over the agent's
browser session when it gets stuck. It ships as iOS, Android, and web from one React
Native codebase.

**It's alpha.** Treat it as a template to build on, which is how the project describes it.

## What's in the box

| Surface | What it does |
|---|---|
| Chat | Streamed AG-UI events, a visible follow-up queue, retained drafts, inline email/browser/PDF/plan cards |
| Agent computer | Persistent browser profiles with a takeover console; optional isolated Linux terminal and editable workspace files |
| Activity | Durable task plans with pause, resume, cancel, retry, approvals, and saved receipts |
| Goals & tracking | Recurring checks on public pages for changes, availability, or a price threshold, with deduplicated alerts |
| Documents | Email attachment → PDF → form values → filled copy → reviewed reply → receipt |
| Gmail & Calendar | Google OAuth adapters; every send or calendar change needs its own stored review |
| Finance | Import a transaction CSV into a spending summary |

## Quick start

Node 24 LTS and pnpm 11.19.0. The sample app needs no model, no Google account, and no
Docker:

```sh
git clone https://github.com/CopilotKit/OpenMuse.git openmuse
cd openmuse
pnpm install --frozen-lockfile
cp .env.example .env
npx copilotkit@latest login
npx copilotkit@latest project select
# put the generated server-only key in .env as CPK_INTELLIGENCE_API_KEY
pnpm dev
```

```sh
pnpm dev:web          # in a second terminal → localhost:8081
```

Then try "Complete the permission slip" in Chat — it fills a PDF form and prepares a
reply against a local mailbox, writing nothing real.

For the browser agent, add `BROWSER_WORKER_URL` and a 32-character `WORKER_TOKEN`:

```sh
pnpm --dir apps/worker exec playwright install chromium
pnpm dev:browser
```

And for the Linux computer:

```sh
docker build -t openmuse-computer:local apps/computer
COMPUTER_ENABLED=true pnpm dev
```

## The one thing to know before you start

**Every deployment requires a CopilotKit Intelligence project key**, including the local
sample. Intelligence handles conversation persistence and replay, it is a separate hosted
service, and — stated plainly in the README — **it is not covered by this repository's
MIT licence**. No key ships with the code.

So "self-hosted" here means the app, the browser, the terminal, and your data are yours,
while the thread layer talks to a vendor service you must sign up for. That may be
perfectly fine; it is not what most people picture when they read MIT on a self-hosting
page, so decide on it before you invest an evening.

## The sandboxing is better than most

Worth noting because agent projects usually hand-wave this:

- The Linux computer runs **nonroot, with no host-directory mounts and no credentials**,
  and its **networking is disabled** — public pages go through the browser worker instead.
- Commands have a 30-second limit and save output and exit-code receipts.
- Google credentials are encrypted at rest; file URLs and browser consoles use short-lived
  signatures.
- **No hidden retry after an uncertain external write.** If a send might have gone
  through, it tells you to check rather than quietly firing again — the failure mode that
  produces duplicate emails in lesser systems.

The limit to respect: this is **one owner behind a shared access key**, not a multi-tenant
auth system. Keep it on loopback, or put HTTPS and network restrictions in front of it.

## Operating it

State lives in `.openmuse/` — embedded PGlite, documents, the signing key, and browser
profiles. That directory *is* the backup. The host has to stay running for background
work, and because PGlite can't be opened by two processes, splitting the task worker out
means moving to real PostgreSQL.

## Where it fits

| Good fit | Poor fit |
|---|---|
| Building a personal agent you control, from a working base | Wanting a finished product today — it's alpha |
| Agent work that must be reviewed before it sends anything | Fully autonomous action without a human gate |
| Keeping mail and browsing off a vendor's servers | A deployment that must avoid every hosted dependency |
| Teams already using CopilotKit and AG-UI | A quick single-binary install |

If what you want is a control plane for *many* agents rather than one personal one, that's
[Paperclip](/docs/ai/paperclip/). If you want the browser automation alone, see
[Browser Use](/docs/ai/browser-use/).

## Next

You've been through every category. To start again, pick another from the
[overview](/docs/).
