---
weight: 110
title: "Vibe Coding Infra"
description: "The free-tier stack behind a solo-built product — hosting, database, cache, storage, auth, email, jobs, search, analytics, monitoring, and getting paid."
icon: "cloud"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

You built the thing in a weekend with an AI agent. Now it has to live somewhere, keep
data, send an email, tell you when it breaks, and take money. That list used to be a
sprint of its own; today every piece has a free tier good enough to launch on, and one
person can wire the whole stack together in an afternoon.

This category is one page per slot in that stack, in the order you'll actually need
them: deploy first, then data, then the services around it, then payments. Every page
states what the free tier really covers and the first bill you'll meet when you outgrow
it — "free" is a starting allowance, not a promise, and it's cheaper to know the ceiling
before your launch day traffic finds it for you.

| Slot | Pick | Free tier in one line |
|---|---|---|
| Backend + frontend hosting | [Railway](/docs/vibe-infra/railway/) | $5 trial credit, then $1/month — real apps need Hobby at $5 |
| Multi-region containers | [Fly.io](/docs/vibe-infra/fly-io/) | None — pay-as-you-go from about $2/month |
| Frontend-only hosting | [Vercel](/docs/vibe-infra/vercel/) | 100 GB bandwidth, non-commercial projects only |
| Edge compute and static sites | [Cloudflare Workers](/docs/vibe-infra/cloudflare-workers/) | 100K requests/day, unlimited bandwidth |
| Database | [Postgres](/docs/vibe-infra/postgres/) | ~0.5 GB on Neon or Supabase, forever |
| Serverless Postgres with branching | [Neon](/docs/vibe-infra/neon/) | A project, scale-to-zero, a monthly compute budget |
| Backend in a box | [Supabase](/docs/vibe-infra/supabase/) | Two projects — free ones pause after a week idle |
| Cache and rate limiting | [Upstash](/docs/vibe-infra/upstash/) | 256 MB, 500K commands/month |
| File storage | [Cloudflare R2](/docs/vibe-infra/cloudflare-r2/) | 10 GB, and zero egress fees at any size |
| Authentication | [Clerk](/docs/vibe-infra/clerk/) | Tens of thousands of users, billed as retained users |
| Email | [Resend](/docs/vibe-infra/resend/) | 3,000/month, 100/day |
| Background jobs and cron | [Inngest](/docs/vibe-infra/inngest/) | Free indefinitely at side-project volume |
| Search | [Meilisearch](/docs/vibe-infra/meilisearch/) | Free to self-host; paid cloud |
| Product analytics and flags | [PostHog](/docs/vibe-infra/posthog/) | 1M events/month, plus replay and flag allowances |
| Error monitoring | [Sentry](/docs/vibe-infra/sentry/) | 5,000 errors/month, 1 user |
| Payments, global | [Lemon Squeezy](/docs/vibe-infra/lemon-squeezy/) | $0 up front, 5% + $0.50 per sale |
| Payments, Korea | [Groble](/docs/vibe-infra/groble/) | $0 up front, 4.4% per sale, no business registration |

The two payment pages are the odd ones out: nothing is charged until you make a sale, so
they are free in the sense that matters — you can set them up before you have a single
customer. Both are **merchants of record**, which means the platform, not you, is the
legal seller and the one that owes sales tax and VAT. For a solo builder that is the
single biggest reason to give up a few percent.

## A caution before you sign up for nine things

Add a service the day the previous one hurts. A frontend on Vercel talking to Postgres
is a complete product; cache, object storage, and a monitoring dashboard are answers to
problems you may not have yet, and every extra account is another dashboard, another set
of secrets, and another thing to migrate later.
