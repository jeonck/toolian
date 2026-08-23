---
weight: 11070
title: "Sentry"
description: "Error monitoring that shows you the stack trace, the release, and the user — instead of a screenshot of a white page."
icon: "bug_report"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

Production failures reach you in one of two ways: a user says "it's broken" with no
detail, or a tool tells you which line threw, on which release, for how many people.
Sentry is the second. It catches unhandled exceptions on the server and in the browser,
groups them into issues, and keeps the context — request, breadcrumbs, release,
environment — attached to each one.

## Free tier

5,000 errors per month, 1 user, 30-day retention. When the quota runs out Sentry drops
events until the next cycle rather than billing you — safe, but it means a noisy bug can
eat the month in a day. The quota discipline below is not optional on the free plan.

## Install

The wizard does the whole setup — config files, source maps, the `.env` entry:

```bash
npx @sentry/wizard@latest -i nextjs     # or: react, node, sveltekit, remix
```

Manually, for a Node service:

```bash
npm install @sentry/node
```

```js
import * as Sentry from "@sentry/node";

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.NODE_ENV,
  release: process.env.RAILWAY_GIT_COMMIT_SHA,   // or the Vercel equivalent
  tracesSampleRate: 0.1,                          // sample, don't record everything
});
```

Python is the same shape:

```bash
pip install "sentry-sdk[fastapi]"
```

```python
import sentry_sdk

sentry_sdk.init(dsn=os.environ["SENTRY_DSN"], traces_sample_rate=0.1)
```

## Source maps, or the stack trace is useless

Minified frontend code produces traces like `a.b is not a function` at column 4,891.
Upload source maps as part of the build and you get your own file and line back. The
wizard configures this; if you set it up by hand, the release name in `Sentry.init` must
match the one you uploaded maps for — that mismatch is the single most common reason
traces stay unreadable.

## Make an issue answerable

An error without context is a puzzle. Add the two things you'll always want:

```js
Sentry.setUser({ id: user.id });          // how many people, and which ones
Sentry.setTag("feature", "checkout");     // filterable in the issue list

try {
  await risky();
} catch (err) {
  Sentry.captureException(err, { extra: { orderId } });
  throw err;
}
```

Never put passwords, tokens, or card details in tags or extras — they're stored and
visible to anyone with access to the project.

## Protecting the 5,000

- **Sample traces, not errors.** `tracesSampleRate: 0.1` keeps performance data cheap;
  leave error capture at full rate.
- **Filter the noise.** Browser extensions, cancelled fetches, and bot traffic generate
  events that will never be fixed. Use `ignoreErrors` and inbound filters.
- **Set a spike protection rule** so one runaway loop can't consume the month.
- **Resolve issues properly.** A resolved issue that recurs reopens with the new release
  attached, which is how you learn a fix didn't work.

## Alerts you'll keep

Default alerts are chatty enough that people mute them, and a muted alert is worse than
none. Two rules are usually right: **a new issue in production**, and **an existing issue
crossing a threshold** — 50 events in an hour, say. Send those to email or Slack;
everything else can wait for you to open the dashboard.

## Next

The infrastructure works. Now get paid →
[Lemon Squeezy](/docs/vibe-infra/lemon-squeezy/)
