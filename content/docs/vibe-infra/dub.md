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

| Plan | Price | New links/mo | Domains | Users | Analytics retention |
|---|---|---|---|---|---|
| Business | $90/mo | 10K | 100 | 10 | 3 years |
| Advanced | $300/mo | 50K | 250 | 20 | 5 years |
| Enterprise | Custom | Unlimited | Unlimited | Unlimited | Unlimited |

API access and conversion tracking are on every paid plan (rate limits 1,200/min →
3,000/min); SSO is Enterprise only. Partner payout ceilings scale $2.5K → $15K →
unlimited per month.

Note the shape of that table: unlike most of this category, Dub's entry price is a real
monthly bill, not a free tier you grow out of. Check dub.co for current free/trial terms
before you plan around it.

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
| Launching a referral or affiliate program without building one | Pre-revenue side projects on a $0 budget |
| Mobile apps needing deep links that survive install | Teams already deep in an enterprise martech stack |

## Next

When something breaks, you want to hear it from a tool, not a user →
[Sentry](/docs/vibe-infra/sentry/)
