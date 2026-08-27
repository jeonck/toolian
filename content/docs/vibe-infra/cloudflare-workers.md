---
weight: 11040
title: "Cloudflare Workers"
description: "Code that runs in every Cloudflare city with no server to keep warm — plus Pages, KV, D1, and Queues around it."
icon: "bolt"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

A serverless function normally means a container somewhere that boots when a request
arrives. [Cloudflare Workers](https://workers.cloudflare.com/) doesn't do that: your code
runs in a V8 isolate inside Cloudflare's edge network, in whichever city the request hit,
with effectively no cold start.

The free tier is one of the last genuinely generous ones on the internet: **100,000
requests a day**, unlimited bandwidth, and it hasn't moved in years.

## Write and deploy one

```bash
npm create cloudflare@latest my-worker
cd my-worker
npx wrangler dev            # local, with the real runtime
npx wrangler deploy
```

```js
export default {
  async fetch(request, env, ctx) {
    const url = new URL(request.url);
    if (url.pathname === "/api/time") {
      return Response.json({ now: Date.now(), city: request.cf?.city });
    }
    return new Response("Hello from the edge");
  },
};
```

Deploy takes seconds and the result is live in every Cloudflare location. `request.cf`
carries geo information you'd otherwise need a lookup service for.

## The pieces that come with it

| Product | What it is | Free tier |
|---|---|---|
| **Workers** | The compute | 100K requests/day |
| **Pages** | Static sites and frontend frameworks, Git-connected | Unlimited sites, generous builds |
| **KV** | Eventually-consistent key-value store, read-optimised | 100K reads/day |
| **D1** | SQLite as a managed database | 5M rows read/day |
| **R2** | Object storage, no egress fees | [Its own page](/docs/vibe-infra/cloudflare-r2/) |
| **Queues** | Producer/consumer messaging | Paid plan |
| **Durable Objects** | Single-instance stateful coordination | Paid for SQLite-backed |

Bindings connect them without credentials — you declare them in `wrangler.toml` and they
arrive as `env`:

```toml
name = "my-worker"
main = "src/index.js"
compatibility_date = "2026-08-01"

[[kv_namespaces]]
binding = "CACHE"
id = "..."

[[d1_databases]]
binding = "DB"
database_name = "app"
database_id = "..."
```

```js
await env.CACHE.put("key", "value", { expirationTtl: 300 });
const { results } = await env.DB.prepare("SELECT * FROM items WHERE id = ?").bind(id).all();
```

No connection string in an environment variable, no secret to rotate. For a small app,
that alone removes a category of work.

## The constraint that decides everything

**Workers is not Node.** It's a V8 isolate with web APIs, and although the Node
compatibility layer covers a lot now, anything needing raw TCP sockets, native modules,
long CPU time, or the filesystem does not belong here. CPU time per request is limited —
generous on paid plans, 10ms on free — while time spent *waiting* on I/O is not.

So: request handling, API routes, auth checks, redirects, image transforms, streaming
proxies, and glue between services — excellent. Video encoding, a long report build, or
anything holding a Postgres connection pool — put it on
[Railway](/docs/vibe-infra/railway/) or [Fly.io](/docs/vibe-infra/fly-io/) and call it
from here.

If you do talk to Postgres, use a driver that speaks HTTP — [Neon](/docs/vibe-infra/neon/)
ships one for exactly this reason — or Hyperdrive, which pools connections on
Cloudflare's side.

## Pages, for the frontend half

Connect a repository and Pages builds and serves it, with a preview URL per branch —
the same shape as [Vercel](/docs/vibe-infra/vercel/), with no commercial-use restriction
and unlimited bandwidth. Next.js and other frameworks run through the Workers runtime, so
the same constraint above applies to their server code.

## Next

Every app needs somewhere to keep data → [PostgreSQL](/docs/vibe-infra/postgres/)
