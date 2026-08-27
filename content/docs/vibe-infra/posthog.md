---
weight: 11140
title: "PostHog"
description: "Product analytics, session replay, and feature flags in one tool — with a free tier big enough to be the only one you need."
icon: "insights"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Server metrics tell you the service is healthy. They cannot tell you that eighty percent
of people abandon the second step of signup, or that the feature you shipped last month
has four users.

[PostHog](https://posthog.com/) covers that side: product analytics, funnels, session
replay, feature flags, experiments, and surveys in one place. It's open source, can be
self-hosted, and the cloud free tier — a million events a month, plus allowances for
replays and flags — is large enough that many small products never pay.

## Install

```bash
npm install posthog-js
```

```js
import posthog from 'posthog-js';

posthog.init(process.env.NEXT_PUBLIC_POSTHOG_KEY, {
  api_host: 'https://eu.i.posthog.com',       // or us.i.posthog.com
  person_profiles: 'identified_only',          // cheaper, and usually what you want
});

posthog.identify(user.id, { email: user.email, plan: user.plan });

posthog.capture('project_created', { template: 'blank', source: 'onboarding' });
```

Server-side events use `posthog-node` and the same event names — which matters, because
the events that count money usually happen on the server.

## Name events like you'll read them in six months

The most common failure isn't technical. It's ending up with `clicked_button_2`,
`Click Button`, and `button-click` all meaning the same thing.

- **`object_verb`, past tense, snake_case**: `project_created`, `invite_sent`,
  `subscription_upgraded`.
- **Properties instead of new events.** One `project_created` with a `template` property
  beats four event names.
- **Track outcomes, not clicks.** "Signed up" is a fact about your business; "clicked the
  blue button" is a fact about a button that will be redesigned.

Write the list of events down before you instrument, and keep it to a dozen at first.

## The four features worth using in order

1. **Funnels.** Pick the three steps that matter — landed, signed up, did the core thing —
   and look at where people fall out. This is the highest-value hour you'll spend here.
2. **Session replay.** Watch five recordings of people failing at that step. It is
   uncomfortable and it answers the question faster than any dashboard. Mask inputs by
   default, and treat replay as personal data.
3. **Feature flags.** Ship dark, enable for yourself, then a percentage. A flag is also
   the fastest rollback that exists — faster than a redeploy.

   ```js
   if (posthog.isFeatureEnabled('new-checkout')) { /* ... */ }
   ```

4. **Experiments,** once the first three are habits — flags plus a metric and the
   statistics done for you.

## Privacy, which is not optional

You're collecting behaviour tied to identified people. Under GDPR that needs a lawful
basis, a mention in your privacy policy, and a consent path for tracking that isn't
strictly necessary. Two settings do most of the work: choose the **EU cloud region** if
your users are European, and **mask inputs in session replay** so passwords and card
fields never leave the browser. Self-hosting is the option when the data genuinely cannot
leave your infrastructure.

## Where it fits alongside the rest

[Sentry](/docs/vibe-infra/sentry/) tells you the code threw. [Prometheus](/docs/observability/prometheus/)
tells you the system is saturated. PostHog tells you the feature isn't being used. Three
different questions, three tools, and only the last one changes what you build next.

## Next

When something breaks, you want to hear it from a tool, not a user →
[Sentry](/docs/vibe-infra/sentry/)
