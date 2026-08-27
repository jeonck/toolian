---
weight: 11020
title: "Fly.io"
description: "Running your container in several regions at once, close to users — and what it costs now that the free tier is gone."
icon: "flight"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

[Railway](/docs/vibe-infra/railway/) runs your app in a region. [Fly.io](https://fly.io/)
runs it in *several*, and routes each request to the nearest one. For an app with users on
two continents, that's the difference between 40ms and 300ms on every round trip — and
you get it by changing a number, not by rewriting anything.

The unit is a **Machine**: a fast-booting Firecracker VM running your container image. It
can also stop when idle and start on the next request in a couple of hundred
milliseconds, which is what makes a multi-region app affordable.

## Say this out loud first: there is no free tier

Fly.io removed its free allowances in 2024. New accounts get a short trial, and after
that it's pay-as-you-go — roughly a couple of dollars a month for one small always-on
machine, and $10–20 for a typical app with a database.

That's cheap, and it is not free. If free is the requirement, this is the page to skip:
[Railway](/docs/vibe-infra/railway/)'s Hobby plan or Cloudflare's genuinely free tiers
are the alternatives. Fly.io earns its cost when geography is the problem.

## Deploy

```bash
brew install flyctl
fly auth signup

cd my-app
fly launch          # detects the stack, writes fly.toml, offers a Postgres
fly deploy
fly open
```

`fly launch` reads your project — Dockerfile, or a framework it recognises — and
generates `fly.toml`. The parts you'll edit:

```toml
app = "my-app"
primary_region = "iad"

[http_service]
  internal_port = 8080
  force_https = true
  auto_stop_machines = "stop"     # scale to zero when idle
  auto_start_machines = true
  min_machines_running = 0        # 1 if a cold start is unacceptable

[[vm]]
  memory = "512mb"
  cpu_kind = "shared"
  cpus = 1
```

`auto_stop_machines` is the flag that decides your bill. With `min_machines_running = 0`,
an idle app costs almost nothing and pays a cold start on the next request; set it to 1
per region for the apps where that matters.

## Going multi-region

```bash
fly scale count 3 --region iad,fra,syd
fly status
fly logs
fly ssh console            # a shell inside the running machine
```

Three commands and the app runs on three continents behind one anycast address. The
part that isn't automatic is **your data**: reads near the user only help if the data is
near the user too. Fly Postgres offers read replicas with writes forwarded to a primary
region — workable, but it changes your app's assumptions, and "replica lag" becomes a
thing you have to reason about.

A pragmatic middle path: app servers in several regions, one database region, and
[Upstash](/docs/vibe-infra/upstash/) or a CDN in front of the reads that dominate.

## What it's good and bad at

| Good fit | Poor fit |
|---|---|
| Latency-sensitive apps with users far apart | A single-region CRUD app — simpler hosts win |
| Long-running processes, WebSockets, queues | Static sites — [Vercel](/docs/vibe-infra/vercel/) or Pages are free |
| Anything already containerised | Teams who don't want to think about regions |
| Bursty workloads that can scale to zero | A first deploy on a zero budget |

Two operational notes worth knowing before you commit: Fly Postgres is an *unmanaged*
Postgres you run, not a managed service with someone else's pager — use
[Neon](/docs/vibe-infra/neon/) or Supabase if you'd rather not own that. And volumes are
tied to a single machine in a single region, so persistent local storage does not
replicate; object storage is the answer for anything that must survive.

## Next

Push-to-deploy hosting for the frontend → [Vercel](/docs/vibe-infra/vercel/)
