---
weight: 12050
title: "ElevenLabs"
description: "Script to natural speech in seconds, voice cloning from a short sample — and the consent rules that come with it."
icon: "record_voice_over"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

A demo video needs narration. Someone has to record it, re-record the two sentences that
changed, and re-record everything again when the feature is renamed.
[ElevenLabs](https://elevenlabs.io/) removes that loop: paste the script, pick a voice,
get audio — and when the script changes, regenerate rather than rebook the studio.

The output is good enough that the interesting questions are no longer about quality.
They're about permission, which is where the rest of this page goes.

## First run

Sign up, open the speech tool, pick a voice from the library, paste a paragraph.
The free tier gives a monthly credit allowance — around ten minutes of speech — which is
plenty to decide whether it works for you.

Three settings do most of the work:

| Setting | Effect |
|---|---|
| **Stability** | Low is expressive and variable; high is consistent and flatter. Documentation narration wants high; a character wants low |
| **Similarity** | How closely it holds to the source voice |
| **Style / speed** | Exaggeration and pacing — small changes go a long way |

Punctuation is your director's notation. Commas and full stops become real pauses,
paragraph breaks become longer ones, and rewriting a sentence to be shorter fixes more
delivery problems than any slider.

## Voice cloning, and the rules that come with it

Instant Voice Cloning builds a usable voice from a short sample — minutes of audio, not
hours — and Professional cloning takes longer samples for a closer match. It is a paid
feature; the free tier is stock voices only.

Before you clone anything:

- **Only clone your own voice, or one you have explicit, documented permission to use.**
  A person's voice is protected by personality rights in most jurisdictions, and consent
  given for one project isn't consent for the next.
- **Label synthetic speech.** The EU AI Act's transparency obligations for AI-generated
  voice and deepfake content apply from **2 August 2026**; publishing synthetic speech as
  if it were a real recording is the thing the rules exist to stop.
- **Never impersonate.** A cloned voice of a public figure, a colleague, or a customer is
  not a demo — it's the raw material of fraud, and the platform's terms and the law both
  treat it that way.

Used within those lines it's genuinely useful: narrate your own documentation in your own
voice without recording each revision, keep one consistent narrator across a video series,
or dub a demo into another language.

## Commercial use

The free tier does not carry commercial rights and expects attribution; paid tiers do
carry them. If the audio goes into anything you charge for, is sponsored, or represents a
company, you need the paid tier — this is the most common mistake with the tool, and the
easiest to avoid.

## Where else it shows up

The same API sits behind a lot of the video and agent tooling elsewhere on this site —
[OpenMontage](/docs/ai/openmontage/) can use it for narration, though it defaults to a
local engine that costs nothing. If you're generating a lot of audio programmatically,
compare the per-character API price against a local model before wiring it in.

## Next

The same job with direction instead of sliders, free in a browser →
[Gemini TTS](/docs/ai-media/gemini-tts/)
