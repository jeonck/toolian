---
weight: 11060
title: "Neon"
description: "Serverless Postgres that scales to zero and branches like Git — a database per pull request, in a second."
icon: "storage"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Postgres was designed for a server that stays up. [Neon](https://neon.com/) separates
storage from compute and rebuilds the developer experience around that: the compute
sleeps when nobody's querying, wakes in well under a second, and **branches** — a full
copy-on-write clone of your data, created instantly and costing almost nothing until you
change something.

It's still ordinary Postgres. Your driver, your migrations, your extensions — `pgvector`
included — all work unchanged. Databricks acquired Neon in 2025; the service kept its
shape and got cheaper.

## Connect

```bash
psql "postgresql://user:pass@ep-cool-name-123456.us-east-2.aws.neon.tech/neondb?sslmode=require"
```

Two connection strings are offered and the difference matters:

- The **pooled** one (`-pooler` in the host) for anything running per request — serverless
  functions, edge handlers. Postgres charges memory per connection and serverless opens a
  lot of them.
- The **direct** one for migrations, which need a session that pooling won't give them.

For [Cloudflare Workers](/docs/vibe-infra/cloudflare-workers/) and other runtimes with no
raw TCP, there's an HTTP driver:

```bash
npm install @neondatabase/serverless
```

```js
import { neon } from '@neondatabase/serverless';
const sql = neon(process.env.DATABASE_URL);
const rows = await sql`SELECT id, title FROM posts WHERE published = true`;
```

## Branching is the reason to be here

```bash
npm install -g neonctl
neonctl branches create --name pr-482 --parent main
neonctl connection-string pr-482
neonctl branches delete pr-482
```

A branch is a copy-on-write clone of the parent at a point in time. Created in about a
second, whatever the size of the database, and billed only for what diverges. Three
things this makes routine:

- **A database per pull request.** CI creates a branch from production-shaped data, runs
  migrations and tests against it, and deletes it when the PR closes. Your integration
  tests stop running against an empty schema.
- **Rehearsing a migration on real data** before it touches production, at no meaningful
  cost.
- **Point-in-time recovery as a branch.** Someone ran the wrong `UPDATE`? Branch from
  twenty minutes ago, read the rows as they were, and repair — without restoring over
  anything.

## The free tier, and the trade

The free plan gives a project with a small storage allowance and a monthly compute budget,
with scale-to-zero on by default. Read the two consequences honestly:

- **Scale to zero means a cold start.** Sub-second, but the first request after idle is
  slower. For a hobby app that's fine; for a checkout flow, keep the compute pinned on a
  paid plan.
- **Compute is metered.** An always-busy app on the free tier will exhaust the budget
  before it exhausts storage.

## Neon or Supabase?

Both offer free Postgres, and they're aimed at different things.

| | Neon | [Supabase](/docs/vibe-infra/supabase/) |
|---|---|---|
| It is | A database | A database plus auth, storage, realtime, functions |
| Signature feature | Branching, scale to zero | The batteries-included client |
| Idle behaviour | Sleeps, wakes on connect | Free projects pause after about a week |
| Pick it when | You want Postgres and nothing else | You want a backend without writing one |

Choosing Neon means picking your own auth and storage — [Clerk](/docs/vibe-infra/clerk/)
and [R2](/docs/vibe-infra/cloudflare-r2/) are the usual companions on this site.

## Next

The other end of the trade: a whole backend in one project →
[Supabase](/docs/vibe-infra/supabase/)
