---
weight: 5090
title: "HyperFrames"
description: "Writing a video as HTML, CSS, and a paused timeline — a rendering framework built so an agent can author the file."
icon: "html"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

Ask an agent to make a video and it has to drive a timeline UI it cannot see. Ask it to
write HTML and it is on home ground. [HyperFrames](https://github.com/heygen-com/hyperframes)
takes the second route: a composition is a web page — HTML, CSS, media elements, a
JavaScript animation timeline — and the framework renders it to a deterministic MP4 with
headless Chrome and FFmpeg.

It came out of HeyGen and is Apache 2.0. The tagline is the whole design: *write HTML,
render video, built for agents.*

## Why HTML is the right source format here

Every property a video needs — layout, type, colour, easing, layering — is a thing the
web already does, and a thing an agent already knows how to write. The output is
deterministic, so the same file renders the same frames every time; and because the
source is text, a video **diffs and reviews like code**. Changing the headline in a
launch clip becomes a one-line pull request rather than reopening a project file.

## A composition

```html
<div id="stage" data-composition-id="launch" data-start="0"
     data-width="1920" data-height="1080">

  <video class="clip" data-start="0" data-duration="6"
         data-track-index="0" src="intro.mp4" muted></video>

  <h1 id="title" class="clip" data-start="1"
      data-duration="4" data-track-index="1">Launch day</h1>

  <script src="https://cdn.jsdelivr.net/npm/gsap@3/dist/gsap.min.js"></script>
  <script>
    const tl = gsap.timeline({ paused: true });
    tl.from("#title", { opacity: 0, y: 40, duration: 0.8 }, 1);
    window.__timelines = window.__timelines || {};
    window.__timelines.launch = tl;
  </script>
</div>
```

Two conventions carry the whole model. `data-start` and `data-duration` place an element
on the timeline, and `data-track-index` layers it. The animation itself must be a
**paused, seekable timeline** — GSAP, CSS animations, Lottie, Three.js, WAAPI all
qualify — because the renderer doesn't record playback, it seeks to each frame and
captures it. Anything driven by `setTimeout` or a live clock will not survive that.

## Run it

```bash
npx hyperframes init my-video    # scaffold a project
npx hyperframes preview          # browser preview with live reload
npx hyperframes render           # encode to MP4
npx hyperframes lint             # catch timing and structure problems first
```

Requirements are Node 22+, FFmpeg, and a Chromium the renderer can drive. `preview` is
the loop you'll live in: it's an ordinary page in an ordinary browser, so DevTools works
on your video.

For anything long, rendering distributes — `hyperframes lambda deploy` then
`lambda render` splits frames across AWS Lambda, and `hyperframes cloud render` hands the
job to HeyGen's hosted renderer. Local rendering is the default and needs neither.

## The agent side

The framework ships **skills** rather than expecting an agent to infer the conventions:

```bash
npx skills add heygen-com/hyperframes
```

That installs a `/hyperframes` router that dispatches to task-specific skills —
a product launch video, a faceless explainer, turning a merged pull request into a clip.
The agent gets the composition patterns, the timing rules, and the lint loop, which is
the difference between an agent that produces a valid MP4 and one that produces a page
that renders black.

## Where this beats the alternatives

| Want | Reach for |
|---|---|
| A video authored and reviewed as code, in your CI | HyperFrames |
| A full production pipeline — research, script, footage, voice | [OpenMontage](/docs/ai/openmontage/) |
| One generated clip from a text prompt | An image or video model, not either of these |

The two compose, and OpenMontage can use HyperFrames as its composition layer. Think of
HyperFrames as the renderer and OpenMontage as the studio around it.

The obvious use is the one nobody sets up: a release video generated from the changelog
on every tag, in your brand's fonts and colours, from a template your team reviews once.
The work stops being a video-editing task and becomes a build step.

## Two things to check

- **Deterministic means deterministic.** Live data, random values, and unpaused
  animations all break frame-by-frame seeking. If a preview looks right and the render
  doesn't, that's the first place to look.
- **Fonts and media must resolve at render time.** A webfont that loads from a CDN
  during preview and fails inside the headless browser is the classic silent failure —
  self-host the assets your composition depends on.

## Next

An agent is only as useful as what it can reach → [Composio](/docs/ai/composio/)
