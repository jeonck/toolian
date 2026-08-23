---
weight: 12020
title: "Krea"
description: "A real-time canvas: sketch on the left, watch the finished image redraw on the right as you move."
icon: "brush"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

Prompt-only generation is a slot machine — write, wait, look, rewrite. [Krea](https://krea.ai/)
closes that loop. Its **Realtime Canvas** puts a rough sketch or a shape on one side and a
rendered image on the other, and it re-renders continuously as you draw. Move a rectangle
and the building moves. Scribble a darker patch and the shadow follows.

That changes what the tool is for. You stop describing a composition in words and start
*arranging* it, which is much closer to how anyone actually decides what a picture should
look like.

## Start here

Sign up (the free tier gives a daily allowance of compute units, no card needed), open
**Realtime**, and try the workflow that shows the point in about a minute:

1. Block out the composition with crude shapes — a rectangle for a screen, a blob where
   the subject goes.
2. Write a short prompt describing the scene, not the details.
3. Drag your shapes around and watch the render follow.
4. When the layout is right, send it to the higher-quality **Enhance** step for the final.

Screenshots and photos work as the input too, which is the trick worth stealing: paste a
screenshot of your app, describe the surrounding scene, and get a product shot with your
real UI in it.

## The tools that earn their keep

| Tool | What it does |
|---|---|
| **Realtime** | The live sketch-to-image canvas |
| **Enhance / Upscale** | Add resolution and detail to a finished frame |
| **Train** | Teach a style or subject from your own images, then generate in it |
| **Video** | Animate a still, or generate short clips |

The **Train** feature is the one teams underuse: a handful of reference images gets you a
consistent illustration style across a whole blog, instead of every post looking like a
different site.

## The trade-off to plan around

Krea runs on compute units. The free daily allowance is enough to learn the tool and make
the occasional asset; a real session of iterating burns through it, and realtime mode
consumes them continuously while you draw. Paid tiers start around $9–10/month for a
monthly pool, and units on individual plans don't roll over.

Practical consequence: **block out the composition first, enhance last.** Time spent
sitting on the realtime canvas thinking is time spent spending.

Commercial rights come with the paid tiers rather than the free one — check the current
terms before shipping anything client-facing.

## Krea or Raphael?

[Raphael](/docs/ai-media/raphael/) is for "I need an image, any decent image, now."
Krea is for "I know roughly what this should look like and I want to steer it." If you
find yourself re-rolling a prompt for the fifth time, that's the signal to switch.

## Next

Whatever made the image, it's probably too small → [Magnific](/docs/ai-media/magnific/)
