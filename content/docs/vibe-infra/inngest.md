---
weight: 11120
title: "Inngest"
description: "Background jobs, retries, and scheduled work for apps with no server to run a worker on."
icon: "pending_actions"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Serverless hosting removes the server, and with it the place your background work used
to live. A request handler can't spend ninety seconds generating a report, retry a failed
webhook tomorrow, or run something every night at 3am — and reaching for Redis, a queue,
a worker process, and a cron container undoes most of the reason you went serverless.

[Inngest](https://www.inngest.com/) puts that machinery behind an HTTP endpoint you
already have. You write functions; it remembers state, retries with backoff, sleeps for a
week if you ask, and shows you every run.

## A function

```bash
npm install inngest
```

```ts
import { Inngest } from "inngest";
export const inngest = new Inngest({ id: "my-app" });

export const onSignup = inngest.createFunction(
  { id: "welcome-sequence", retries: 4 },
  { event: "user/signed.up" },
  async ({ event, step }) => {
    await step.run("send-welcome", () =>
      sendEmail(event.data.email, "welcome")
    );

    await step.sleep("wait-three-days", "3d");

    const active = await step.run("check-activity", () =>
      hasCreatedProject(event.data.userId)
    );

    if (!active) {
      await step.run("send-nudge", () => sendEmail(event.data.email, "getting-started"));
    }
  }
);
```

```ts
await inngest.send({ name: "user/signed.up", data: { userId, email } });
```

Every `step.run` is checkpointed. If the third step throws, the first two are not repeated
— they're replayed from memory — and the failing one retries with backoff. `step.sleep`
costs nothing while it waits, so a three-day delay is not a process sitting idle.

That checkpointing is the whole idea: a multi-day workflow written as one readable
function, running on hosting that forgets everything between requests.

## Scheduled work

```ts
export const nightly = inngest.createFunction(
  { id: "nightly-report" },
  { cron: "0 3 * * *" },
  async ({ step }) => { /* ... */ }
);
```

Cron without a machine to run it on — and unlike your platform's cron trigger, a failure
retries and is visible in a run log with the arguments and the error.

```bash
npx inngest-cli@latest dev      # local dev server with the full UI
```

## Where it fits

| Job | Good fit? |
|---|---|
| Onboarding and drip sequences | Yes — the sleep-and-branch pattern |
| Webhook processing with retries | Yes |
| Report generation, exports, imports | Yes |
| Nightly cleanup and reconciliation | Yes |
| Fan-out across thousands of items | Yes, with concurrency and throttle controls |
| Sub-100ms work in the request path | No — just do it inline |
| Very high-throughput streaming | No — that's Kafka's job |

The free tier covers a real side project indefinitely; check the current run limits before
you build something high-volume on it. Trigger.dev is the closest alternative, your cloud
provider's queue plus a worker is the DIY route, and Inngest can also be self-hosted if
you'd rather own it.

## One habit that saves you later

Make every step **idempotent**. Retries are the feature, which means each step may run
more than once: send-with-an-idempotency-key, upsert instead of insert, check before you
charge. A retry that emails a customer twice is the bug this tool will find in your code.

## Next

Users need to be findable, and so does your content →
[Meilisearch](/docs/vibe-infra/meilisearch/)
