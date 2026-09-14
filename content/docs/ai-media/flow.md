---
weight: 12085
title: "Google Flow"
description: "Google's AI filmmaking workspace, now on the phone where your footage already lives — shoot or pick a clip, queue generations in the background, finish on the desktop."
icon: "movie_filter"
date: "2026-09-14"
lastmod: "2026-09-14"
draft: false
---

[Flow](https://labs.google/flow) is Google's AI filmmaking workspace: Veo for video,
Nano Banana for images, Gemini Omni for generation and editing, and project tools that
hold multi-shot sequences together rather than handing you one clip at a time.

It started as a browser app. The interesting change is that it's now an **iPhone app** —
which sounds like a minor port and isn't, for two reasons.

## The phone is where the footage is

The app pulls from the **camera roll and the live camera**. Instead of describing a
kitchen, you photograph your kitchen; instead of prompting for a person walking, you use
the ten seconds you already shot. Grounding a generation in your own material is the
difference between "AI video" and "footage of the thing I actually meant", and a laptop
is the worst possible place to do it because the source material is on the device in your
pocket.

## Generation stopped being a waiting room

The second change is scheduling. You can **queue several generations at once, let them run
in the background, and get a notification when they're done.** Video generation takes
minutes, and the desktop pattern has always been to sit and watch a progress bar. Queue
and forget turns it into something you do between other things — which, in practice, is
the difference between trying four variations and settling for the first one that didn't
fail.

Projects and assets **sync with the desktop**, so the natural workflow is capture and
queue on the phone, assemble and finish on the big screen. The library is the same
library on both.

The iPhone build went out as a TestFlight beta in July 2026 and is now on the App Store,
free with in-app purchases.

## What the credits actually cost

Flow runs on credits, and there's a free tier before you decide anything:

| Plan | Per month | Credits |
|---|---|---|
| Free | — | 50 a day |
| Google AI Plus | $4.99 | 200 |
| Google AI Pro | $19.99 | 1,000 |
| Google AI Ultra | $99.99 / $199.99 | 10,000 / 25,000 |

A fast generation runs roughly 20 credits and a top-quality clip around 100, with Ultra
paying roughly half per video. So AI Pro's 1,000 credits is something like ten quality
clips or fifty quick ones a month — and the number that matters isn't credits per clip,
it's **credits per clip you'd actually use.** Budget for re-rolls: the first result is
rarely the keeper, and that ratio, not the sticker price, decides which tier you need.

As with everything in this category, treat these figures as the shape of the offer rather
than a quotation — Google has re-tiered this more than once.

## Where it sits

Gemini Omni Flash currently leads the text-to-video arena and generates synchronised
audio in one pass — see [Best in Class](/docs/ai-media/best-in-class-2026-09/) for the
rest of that field. Flow is the workspace wrapped around those models: shots, scenes and
a library, rather than a prompt box.

It is not an editor. When you have the clips and need a timeline, cuts and an export
preset, that's [Drift](/docs/ai-media/drift/) — and Drift's MCP server means an agent can
work that timeline, which Flow doesn't offer.

## Next

Which model currently leads each of these tasks →
[Best in Class, September 2026](/docs/ai-media/best-in-class-2026-09/)
