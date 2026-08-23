---
weight: 12010
title: "Raphael"
description: "Text-to-image in a browser tab with no account, no credit card, and no per-image counter."
icon: "image"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

Most image generators want a sign-up before you find out whether the output is any good.
[Raphael](https://raphael.app/) skips that: open the page, type a prompt, get an image in
seconds. No account, no card, and the basic model has no generation counter — which makes
it the right thing to reach for when you need a placeholder hero image *now* and don't
want another subscription.

Under the hood it routes to current open and hosted models rather than running one of its
own, so quality tracks whatever is good this quarter.

## Get the domain right

The official site is **raphael.app**. There is a crowd of similarly named sites —
`raphael.ai`, `raphaelai.art`, `airaphael.org`, and more — which are not the same
project. Type the domain rather than following a search-result ad, and be sceptical of
any version of this that asks for a card number to "verify" a free tier.

## Getting a usable image

The interface is one text box, so the prompt does all the work.

```
A wide banner illustration of a terminal window floating over a dark desk,
soft rim lighting, muted teal and amber palette, flat vector style,
generous empty space on the right for a headline
```

Four habits that raise the hit rate:

- **Say what kind of picture it is first** — photo, flat vector, oil painting, 3D render.
  It constrains everything downstream.
- **Name the lighting.** "Soft rim lighting", "overcast", "golden hour" changes more than
  another adjective about the subject.
- **Leave room deliberately.** Asking for empty space on one side gets you something a
  headline can sit on; cropping it in afterwards never looks as good.
- **Generate four, then refine one.** Faster than perfecting the prompt in the abstract.

## What the free tier costs you

Nothing in money, but know the trade: the free path runs on the basic model with a
resolution cap, and paid tiers exist for higher resolution, priority queueing, and
watermark-free output. The exact split has changed more than once — check the site before
you build a workflow on it, and assume anything free is generated at web resolution
rather than print.

For anything you'll actually publish, the practical test is simple: enlarge it to the
size it will appear at. If it falls apart, that's what [Magnific](/docs/ai-media/magnific/)
is for.

## Where it fits, and where it doesn't

| Good fit | Reach elsewhere |
|---|---|
| Placeholder and blog header images | Anything with legible text in the image |
| Concept sketches and mood exploration | Precise brand assets — use a designer or a template |
| Throwaway illustration for a slide | Photos of real people, products, or places |

And the rule that applies to every generator on this page: **don't generate images of real
people**, don't recreate a known brand's look, and check the terms before putting the
output in something you charge for.

## Next

For the version where you draw and it renders as you go → [Krea](/docs/ai-media/krea/)
