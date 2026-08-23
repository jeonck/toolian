---
weight: 5080
title: "OpenMontage"
description: "Turning a coding agent into a video production pipeline — research, script, assets, edit, render — from one sentence."
icon: "movie"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

Most AI video tools generate a clip. Making an actual video is a *pipeline*: research the
topic, write a script, plan scenes, gather footage, cut it, add narration and captions,
render. [OpenMontage](https://github.com/calesthio/OpenMontage) hands that pipeline to a
coding agent — which turns out to be a reasonable thing to do, because a pipeline of
scripted steps with checkpoints is exactly what agents are good at.

You describe the video; the agent runs research → proposal → script → scene plan →
assets → edit → compose, stopping for your approval at each creative checkpoint.

## Setup

```bash
git clone https://github.com/calesthio/OpenMontage.git
cd OpenMontage
make setup
```

Requirements: Python 3.10+, Node.js 18+, FFmpeg, and a coding agent (Claude Code, Cursor,
Copilot, Windsurf, or Codex). `make install-gpu` adds local video models — WAN, Hunyuan,
CogVideo — if you have the card for it.

## Make something

From your agent, in the OpenMontage directory:

```
> Make a 60-second animated explainer about how neural networks learn
> Cut a two-minute documentary montage on the Apollo programme from archive footage
```

Renders land in `projects/<project-name>/renders/final.mp4`.

## It really does run free

The default path needs **no API keys at all**:

| Stage | Free tool |
|---|---|
| Narration | Piper TTS, running locally |
| Footage | Archive.org, NASA, Wikimedia Commons |
| Composition | Remotion (React) or HyperFrames (HTML/GSAP) |
| Assembly and captions | FFmpeg, with word-level timing |

Paid APIs are opt-in and unlock generated video, generated imagery, and better voices;
the project puts a full production at roughly $1–5 and ships budget controls to stop a
run before it surprises you.

Twelve-plus pipelines are included — animated explainer, documentary montage, cinematic
trailer, talking head, avatar spokesperson, screen demo, podcast repurposing,
localisation and dub, character animation.

## Where it fits

For a changelog video, a demo clip, or a launch explainer, this is the difference between
"we'll do it next quarter" and an afternoon. For anything where the *craft* is the point,
it gets you a draft to edit, not a finished film.

## Two things to check first

- **The licence is AGPLv3**, not MIT like the rest of this page. Fine for videos you make
  and publish; if you're thinking of building a product around it, read the terms — the
  copyleft reaches network-delivered services.
- **Archive footage has its own licences.** Public-domain sources are the safe default,
  but "free to download" and "free to use in a monetised video" are different questions.
  Check per clip before publishing commercially.

## Next

Changes made with AI need version control around them →
[Git & Collaboration](/docs/git/)
