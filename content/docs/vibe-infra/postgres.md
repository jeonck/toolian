---
weight: 11050
title: "PostgreSQL"
description: "The default database for a new project, and how the Neon and Supabase free tiers differ once you're past the signup."
icon: "database"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

Pick Postgres and stop thinking about it. It handles relational data, JSON documents,
full-text search, and — with `pgvector` — the embeddings your AI feature needs, so one
database covers the four things a small product usually reaches for. Every host in this
category speaks it, so nothing you write is locked to a vendor.

## Where to run it free

| | [Neon](/docs/vibe-infra/neon/) | [Supabase](/docs/vibe-infra/supabase/) | Railway |
|---|---|---|---|
| Free storage | 0.5 GB per project | 500 MB | Counts against your $1–5 credit |
| Projects | 100 | 2 | Unlimited within the plan |
| Idle behaviour | Scales to zero, wakes on connect | **Paused after ~1 week idle**, manual unpause | Stays up while credit lasts |
| Extras | Branching | Auth, file storage, realtime, edge functions | Sits next to your app |

Both have a page of their own here — [Neon](/docs/vibe-infra/neon/) and
[Supabase](/docs/vibe-infra/supabase/) — and the short version follows.

Choose by what else you need. **Supabase** if you want auth and storage handed to you
and would rather not assemble them; **Neon** if you only want a database and value
branching — a branch is a copy-on-write clone of production data, which makes testing a
migration genuinely safe. **Railway's** Postgres is the least fuss when the app already
lives there, at the cost of eating the same credit as your services.

The Supabase idle pause is the one that bites: a demo you show once a month will be
asleep when you open it.

## Connect

Every provider hands you a connection string. It is a secret — it goes in the host's
environment variables, never in the repository.

```bash
DATABASE_URL="postgres://user:password@host/dbname?sslmode=require"
psql "$DATABASE_URL"
```

Serverless hosts open and drop connections constantly, and Postgres charges memory per
connection, so use the **pooled** string (Neon calls it `-pooler`, Supabase gives you a
port-`6543` transaction-pooler URL) anywhere the code runs per request. Keep the direct
connection for migrations, which need a session that pooling won't give them.

## The commands worth memorising

```sql
\l                          -- list databases
\dt                         -- list tables
\d+ users                   -- describe a table with sizes
\x                          -- toggle expanded output; use it on wide rows
SELECT pg_size_pretty(pg_database_size(current_database()));
```

That last one tells you how close you are to the free-tier ceiling. Run it before you
wonder why writes started failing.

## Migrations, not hand-edits

Whatever the stack — Prisma, Drizzle, Alembic, Rails, plain `.sql` files — schema changes
belong in versioned files that run the same way in every environment.

```bash
npx prisma migrate dev --name add_orders    # write and apply locally
npx prisma migrate deploy                   # apply in CI or on the host
```

A change applied by hand in a dashboard exists in exactly one place, and the next
deployment from a clean environment won't have it.

## Two indexes and a habit

```sql
CREATE INDEX ON orders (user_id);              -- anything you filter or join on
CREATE UNIQUE INDEX ON users (lower(email));   -- how you actually look users up
EXPLAIN ANALYZE SELECT ...;                    -- read this before adding a third
```

A free tier is small enough that a missing index shows up as a slow page long before
storage runs out.

## Backups

Free tiers give short or no retention, so take your own before anything irreversible:

```bash
pg_dump "$DATABASE_URL" -Fc -f backup.dump
pg_restore -d "$DATABASE_URL" backup.dump
```

Thirty seconds, and it is the difference between a bad migration being an inconvenience
and being the end of the project.

## Next

Serverless Postgres that branches like Git → [Neon](/docs/vibe-infra/neon/)
