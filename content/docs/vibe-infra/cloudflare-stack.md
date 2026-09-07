---
weight: 11035
title: "The Cloudflare Stack"
description: "Putting hosting, database, storage, domains, and AI on one platform — and the three bills it removes."
icon: "hub"
date: "2026-09-07"
lastmod: "2026-09-07"
draft: false
---

The default answer for shipping something you built with an agent is
[Vercel](/docs/vibe-infra/vercel/) for the frontend and
[Supabase](/docs/vibe-infra/supabase/) for the backend. It works, and both are excellent.
The case for Cloudflare isn't that they're bad — it's that their pricing has three
dimensions Cloudflare simply doesn't bill on, and for a small team those three are
usually where the surprise arrives.

## The three bills that don't exist here

**Bandwidth.** Cloudflare charges nothing for data transfer on any tier — Workers, Pages,
and R2 alike, at any volume. Vercel's Pro plan includes 1 TB of Fast Data Transfer and
10M edge requests, then bills on demand against your $20 monthly credit. Under a blog
that's irrelevant. Under an image-heavy site, a media gallery, or anything that got
linked somewhere busy, that's the invoice people post screenshots of.

**Seats.** Vercel Pro is a $20/month platform fee including one deploying seat, and
**$20/month for each additional deploying seat**. (Read-only viewer seats are free, which
is fairer than the complaint usually allows — but anyone who deploys costs $20.) Three
people who all ship: $60/month before a single request is served. Cloudflare's Workers
Paid plan doesn't bill per person.

**Projects.** Supabase's free tier is two active projects per organisation, and free
projects pause after a week idle. Pro is $25/month plus $10/month of compute per project,
with $10 of credit covering the first — so two projects is $35/month, five is $65. On
Cloudflare, **$5/month covers your Workers, your D1 databases, and your KV namespaces
together**, not per project.

| Situation | Vercel + Supabase | Cloudflare |
|---|---|---|
| One person, one side project | $0 while it fits both free tiers | $0 |
| Image-heavy site that gets traffic | Transfer billed past the included tier | Bandwidth free |
| Three people deploying | $60/month in seats alone | $5/month |
| Five small projects | $25 + $40 compute | $5/month |

Prices move. Check them before you commit — but the *shape* of the difference has been
stable for years.

## What covers which slot

| Slot | Cloudflare product |
|---|---|
| Frontend hosting | Pages |
| Backend / API | [Workers](/docs/vibe-infra/cloudflare-workers/) |
| SQL database | D1 (SQLite) |
| Key-value | Workers KV |
| Object storage | [R2](/docs/vibe-infra/cloudflare-r2/) — no egress fees |
| Queues, cron, stateful coordination | Queues, Cron Triggers, Durable Objects |
| Domains | Cloudflare Registrar, sold at wholesale cost |
| Model inference and routing | Workers AI, AI Gateway |
| Access control | Cloudflare Access |

The registrar is a quiet win: domains renew at cost, with no first-year-cheap,
third-year-painful pricing and no upsell to privacy protection that should have been
free.

## One CLI, and an agent can drive it

```bash
npm create cloudflare@latest my-app
npx wrangler dev
npx wrangler deploy
npx wrangler d1 create my-db
npx wrangler r2 bucket create my-files
```

Everything lives in one `wrangler.toml`, including the bindings that connect a Worker to
its database and buckets without credentials in environment variables. Because it's one
declarative file and one CLI, an agent can set the whole thing up from a description —
which is genuinely different from clicking through four dashboards. The
[Workers page](/docs/vibe-infra/cloudflare-workers/) has the details.

## What you give up

This is a real trade, not a free lunch.

- **Workers is not Node.** It's a V8 isolate with web APIs. `nodejs_compat` covers a lot
  now, but some npm packages still won't run, and finding out which is a day you didn't
  plan for. Check your dependencies before you port anything.
- **D1 is SQLite, not Postgres.** No `pgvector`, no Postgres extensions, no
  `pg_dump` muscle memory. If your app wants Postgres, keep it on
  [Neon](/docs/vibe-infra/neon/) or Supabase and reach it through Hyperdrive — a
  perfectly good hybrid, just not the one-platform story.
- **The free tier is 100K requests a day and 10 ms of CPU per invocation.** Waiting on
  I/O doesn't count, so most apps fit; anything genuinely CPU-heavy needs the paid plan,
  which raises the ceiling to 30M CPU-milliseconds a month.
- **Next.js is smoother on Vercel.** That's Vercel's own framework on Vercel's own
  runtime; the Cloudflare adapters have improved a lot and still aren't friction-free.
- **One vendor is one outage, one support queue, and one account to lose.** Consolidation
  is the whole benefit and the whole risk. Support at $5/month is documentation and a
  community forum. Keep your data exportable and your domain registrar decision
  reversible.

## Who this is actually for

Reach for Cloudflare when the traffic is bandwidth-shaped, when more than one person
deploys, when you run several small projects instead of one big one, or when you'd rather
manage one bill than six. Stay with the default stack when your app is a Next.js
application with Postgres underneath and you value that path being the well-trodden one.

Both are correct answers. The mistake is only discovering the difference from an invoice.

## Next

The compute layer in detail, with no cold start →
[Cloudflare Workers](/docs/vibe-infra/cloudflare-workers/)
