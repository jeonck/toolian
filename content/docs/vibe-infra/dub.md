---
weight: 11145
title: "Dub"
description: "Open-source link infrastructure — short links with an API, click and conversion attribution, deep links, and a built-in affiliate program."
icon: "link"
date: "2026-09-25"
lastmod: "2026-09-25"
draft: false
---

[PostHog](/docs/vibe-infra/posthog/) tells you what people did once they arrived.
[Dub](https://dub.co/) tells you how they got there — and, if you want, pays whoever
sent them.

Steven Tey built it as a side project out of a specific frustration: Bitly and friends
are closed, dated, and gate anything interesting behind an enterprise quote. The
alternative he shipped is AGPLv3, API-first, and now runs 100M+ clicks a month across
2M+ links — which is also the reason it belongs in this category rather than a list of
marketing SaaS. A short link is infrastructure the moment your product emails, invites,
referral codes, or QR codes depend on it.

## Why a developer would care

**Every link is an API object.** Creating one is a function call, not a form:

```bash
npm i -g dub-cli@latest
dub login
dub shorten https://example.com/very/long/path launch
dub links --search launch
```

```typescript
import { Dub } from "dub";
const dub = new Dub();                       // reads DUB_API_KEY

const link = await dub.links.create({ url: "https://example.com/signup" });
const clicks = await dub.analytics.retrieve({ linkId: link.id, groupBy: "timeseries" });
```

Server-side SDKs cover TypeScript, Go, Python, Ruby, and PHP, with client-side
`@dub/analytics` plus iOS and React Native SDKs. So "generate a tracked link per user"
becomes a line in your signup handler instead of a spreadsheet someone maintains.

**Attribution goes all the way to revenue.** Dub tracks click → lead → sale, not just
clicks. Wire the client SDK and a server-side event and you can answer "which campaign
produced paying customers", which is the question link shorteners normally can't touch.

**Deep links are handled.** One link that opens your iOS or Android app if installed and
the web page if not — including deferred deep linking through install — without you
maintaining universal-link plumbing.

**It doubles as your affiliate program.** Dub Partners issues partner links, tracks
commissions, and runs payouts. For a solo product that's a growth channel you'd
otherwise buy a second SaaS for.

**There's an MCP server.** Remote, OAuth'd, nothing to install:

```
https://mcp.dub.sh/mcp/dub-partners
```

Add it as a custom connector in Claude, or point Claude Code at it, and "create tracked
links for these five campaigns and show me last week's conversions" is one sentence.
A `dub-links` server exists separately for link management.

## What it costs

**There is a free plan**, no credit card: 1,000 tracked events a month, up to 3 custom
domains, unlimited destination edits, and the full analytics breakdown (geo, device,
browser, referrer). The API works on it at 60 requests/minute — enough to build against.

| Plan | Tracked events/mo | API rate limit | Notable unlock |
|---|---|---|---|
| Free | 1,000 | 60/min | 3 domains, no analytics API |
| Pro | 50,000 | 600/min | Analytics API, 10 domains |
| Business | 250,000 | 1,200/min | Events stream, webhooks, **Dub Partners** |
| Advanced | 1,000,000 | 3,000/min | Higher payout ceilings |
| Enterprise | Custom | Custom | SSO/SAML, domain registration API |

Business is $90/mo and Advanced $300/mo; partner payout ceilings run $2.5K → $15K →
unlimited per month. A one-off 14-day trial unlocks paid features with its own caps
(100 links/mo, 5,000 analytics events, 50 partners, $500/mo payouts).

The jump to notice is **Dub Partners starting at Business ($90/mo)**. Short links,
analytics, and conversion tracking are reachable from free; the affiliate program is the
part you pay real money for.

## Self-hosting

AGPLv3, and self-hosting is documented rather than merely permitted — but read the
prerequisites before you commit. It wants a Tinybird account for analytics, Upstash for
Redis, PlanetScale for MySQL, Vercel to run Next.js, and Cloudflare or AWS for storage.
That's five accounts to avoid one bill, and the AGPL obligates source access if you let
others interact with your instance.

Self-host when data residency or per-link volume makes the hosted price absurd. Otherwise
the hosted version is the lazy answer, and the open source is mainly your insurance
against the Bitly problem repeating.

## Where it fits

| Good fit | Poor fit |
|---|---|
| Product links you generate programmatically | One personal link in a bio — use anything free |
| Knowing which channel produced revenue, not clicks | Pure web analytics — [PostHog](/docs/vibe-infra/posthog/) already does that |
| Launching a referral or affiliate program without building one | High link volume on the free plan — 1,000 events goes fast |
| Mobile apps needing deep links that survive install | Teams already deep in an enterprise martech stack |

## Next

When something breaks, you want to hear it from a tool, not a user →
[Sentry](/docs/vibe-infra/sentry/)
