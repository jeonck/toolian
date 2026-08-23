---
weight: 11020
title: "Vercel"
description: "Push-to-deploy hosting for a frontend, with preview URLs per branch — and the licence clause that catches people out."
icon: "change_history"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

If your app is a frontend — a static site, a React or Vue SPA, a Next.js app whose
server work fits in a request — Vercel is the shortest distance from a Git push to a URL
someone else can open. Connect the repository once and every commit builds; every pull
request gets its own preview link.

## The Hobby plan, in full

| Included per month | Amount |
|---|---|
| Bandwidth | 100 GB |
| Edge requests | 1 million |
| Function invocations | 1 million |
| Compute | 4 CPU-hours |
| Build execution | 100 build minutes |

Hitting a limit **pauses the project** rather than billing you, which is the right
failure mode for a side project.

## Read this clause before you launch

The Hobby plan is licensed for **non-commercial personal projects**. Ads, a payment
button, affiliate links, or client work all move you onto Pro at $20/month.

That is a direct collision with the rest of this category: the day you add a
[Lemon Squeezy](/docs/vibe-infra/lemon-squeezy/) checkout to a site hosted on Hobby, you
owe Vercel $20/month. Plan for it, or put the commercial half on
[Railway](/docs/vibe-infra/railway/) — whose $5 Hobby plan has no such restriction — and
keep Vercel for the marketing site.

## Deploy

```bash
npm install -g vercel
vercel                       # first run: link the project, deploy a preview
vercel --prod                # promote to the production domain
vercel env pull .env.local   # bring production variables down for local dev
vercel logs <deployment-url>
```

The dashboard route (**Add New → Project → import from GitHub**) is equivalent and gives
you the Git integration in the same step, which is what you actually want day to day —
the CLI is best kept for one-off deploys and pulling environment variables.

## Environment variables

Set them per environment (Production / Preview / Development) in
**Settings → Environment Variables**. Two rules save a lot of debugging:

- A variable is baked in **at build time**. Change one and you must redeploy; editing it
  in the dashboard does nothing to the running deployment.
- In Next.js, only names prefixed `NEXT_PUBLIC_` reach the browser. Anything else exists
  server-side only — which is exactly where your database URL and API keys belong.

```
DATABASE_URL=postgres://...           # server only
NEXT_PUBLIC_SITE_URL=https://...      # shipped to the browser
```

## Previews are the actual feature

Every branch gets a permanent URL. Send that link instead of a screenshot, and if the
change is bad you close the pull request and nothing was ever promoted. Combined with
the preview environments in [Railway](/docs/vibe-infra/railway/), a reviewer can click
one link and see the full stack as that branch would run it.

## Custom domain

**Settings → Domains → Add**, then point the registrar at Vercel — an `A` record for the
apex, `CNAME` for `www`. TLS is issued automatically; propagation is minutes, not hours,
and the dashboard shows exactly which record is still wrong.

## Where it stops

Vercel functions are request-scoped. A queue consumer, a WebSocket server, a job that
runs for two minutes, or anything holding a pooled database connection wants
[Railway](/docs/vibe-infra/railway/) instead. A common split: marketing site and app
shell on Vercel, the long-lived services on Railway.

## Next

Both hosts need somewhere to keep the data → [PostgreSQL](/docs/vibe-infra/postgres/)
