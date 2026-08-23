---
weight: 11040
title: "Upstash"
description: "Serverless Redis over HTTP — caching, rate limiting, and a queue, without a connection pool to manage."
icon: "memory"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

Redis is the obvious cache, but classic Redis wants a long-lived TCP connection —
awkward on Vercel functions, which may be a hundred short-lived instances. Upstash serves
the same data structures over **HTTP**, so a function does a `fetch`, gets an answer, and
dies without leaking a connection. Pricing is per command rather than per hour, so an
idle database costs nothing.

## Free tier

| Limit | Amount |
|---|---|
| Storage | 256 MB |
| Commands | 500,000 per month |
| Bandwidth | 10 GB, single region |

500K commands is roughly 15K a day. A cache hit is one command, a rate-limit check is
two or three — enough for a real launch, and the dashboard shows the burn rate.

## Set up

Create a database at console.upstash.com, pick the region closest to your app — this
matters more than the plan — and copy the two REST values.

```bash
UPSTASH_REDIS_REST_URL="https://xxx.upstash.io"
UPSTASH_REDIS_REST_TOKEN="AX..."
```

```bash
npm install @upstash/redis
```

```js
import { Redis } from "@upstash/redis";

const redis = Redis.fromEnv();          // reads the two variables above

await redis.set("greeting", "hello", { ex: 60 });   // expires in 60s
const value = await redis.get("greeting");
```

## The three things you'll actually use it for

**Cache an expensive read.** The pattern is always the same — look, miss, compute,
store with an expiry:

```js
async function getStats(userId) {
  const key = `stats:${userId}`;
  const hit = await redis.get(key);
  if (hit) return hit;

  const fresh = await db.expensiveQuery(userId);
  await redis.set(key, fresh, { ex: 300 });   // 5 minutes
  return fresh;
}
```

Always set an expiry. A cache without one is a second database with none of the
guarantees.

**Rate limit a route.** Anything public with an LLM behind it needs this on day one,
before someone finds it and spends your API budget:

```bash
npm install @upstash/ratelimit
```

```js
import { Ratelimit } from "@upstash/ratelimit";

const limiter = new Ratelimit({
  redis,
  limiter: Ratelimit.slidingWindow(10, "60 s"),
});

const { success } = await limiter.limit(ip);
if (!success) return new Response("Slow down", { status: 429 });
```

**Hold a short-lived token.** Email verification codes, one-time links, an in-flight job
status: things that should disappear on their own rather than accumulate in Postgres.

## Keeping inside the limits

- **Cache the query, not the row.** One key holding a rendered result costs one command;
  fetching twenty rows individually costs twenty.
- **Namespace your keys** — `user:42:stats`, not `stats42`. `SCAN` with a prefix is how
  you clear a feature's cache later.
- **Never cache anything you can't recompute.** Free-tier eviction is real; treat every
  key as something that may vanish.
- **Pick one region.** Global replication multiplies both latency benefits and command
  count; a single region is right until you have users on two continents.

## Next

Files don't belong in Redis or Postgres →
[Cloudflare R2](/docs/vibe-infra/cloudflare-r2/)
