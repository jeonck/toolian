---
weight: 12060
title: "Gemini TTS"
description: "Directing a voice in Google AI Studio — scene, style, and pacing as instructions, free in the browser and available as an API."
icon: "graphic_eq"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

[ElevenLabs](/docs/ai-media/elevenlabs/) gives you a voice and three sliders. Google's
speech models take a different approach: you **direct** the read. Describe the scene, say
who is speaking and how, and the model performs it — the current preview, Gemini 3.1
Flash TTS, is built around that idea and is free to use in
[Google AI Studio](https://aistudio.google.com/) with no API key.

## Generate one in the browser

1. Sign in to AI Studio and pick the TTS model under **Audio** in the model selector on
   the right.
2. Use the composer fields rather than one blob of text:
   - **Scene** — where this is happening and what it's for.
   - **Context** — who is speaking, to whom, in what mood.
   - **Script** — the words themselves.
3. Press **Run**. Playback starts while generation is still going, so you hear whether
   the read is right within a second or two rather than waiting for the file.
4. Download the audio when you're happy with it.

You can dictate the script with the microphone instead of typing it, which is quicker
than it sounds for anything conversational.

## Direction is the interface

The controls that matter are words, not knobs.

| Control | Examples |
|---|---|
| **Style** | news anchor, whispered ASMR, amused, empathetic, promotional, deadpan |
| **Pace** | natural, faster, slower, or staccato — one word at a time |
| **Inline tags** | `[whispers]`, `[laughs]`, and similar, placed mid-sentence |
| **Voice** | around thirty prebuilt voices, selectable per speaker |
| **Speakers** | more than one, each with its own voice and direction |

```text
Scene: a short product video, warm and unhurried.
Context: a friendly narrator explaining a feature to a new user.
Script: [warmly] Right — let's get your first project deployed. It takes about a minute.
```

Two habits produce most of the improvement: **describe the situation rather than the
emotion** ("explaining to a nervous first-time user" beats "friendly"), and **punctuate
for breath**. Shorter sentences and real full stops become real pauses.

## Then the API, when it works in the browser

```python
from google import genai
from google.genai import types

client = genai.Client()
result = client.models.generate_content(
    model="gemini-3.1-flash-tts-preview",     # check the current id in the docs
    contents="Read this warmly: Welcome back.",
    config=types.GenerateContentConfig(
        response_modalities=["AUDIO"],
        speech_config=types.SpeechConfig(
            voice_config=types.VoiceConfig(
                prebuilt_voice_config=types.PrebuiltVoiceConfig(voice_name="Kore")
            )
        ),
    ),
)
```

Prototype in AI Studio, then move the prompt that worked into code. Preview model ids
change — pin one you've tested and re-check when you upgrade.

## What to expect from a preview model

- **Keep passages short.** Long scripts in one request are where it stutters or fails.
  Generate paragraph by paragraph and join the files; it's also easier to re-do one line
  than a whole chapter.
- **Preview means preview.** Behaviour, ids, quotas, and availability can change, and a
  model can be withdrawn. Fine for a video this week; think twice before a product
  depends on it.
- **Check the terms for commercial use** and current free-tier limits before you publish
  something you're paid for.

## The rules that don't change with the vendor

Synthetic speech carries the same obligations wherever it comes from: only clone or
imitate a voice you own or have documented permission to use, never present generated
audio as a real recording of a real person, and **label it** — the EU AI Act's
transparency requirements for AI-generated audio apply from 2 August 2026.

## Gemini TTS or ElevenLabs?

| Want | Use |
|---|---|
| Free, fast, highly directable reads in a browser | Gemini TTS |
| A cloned voice, or a consistent narrator across a series | [ElevenLabs](/docs/ai-media/elevenlabs/) |
| Narration in a pipeline with no API cost | [OpenMontage](/docs/ai/openmontage/)'s local Piper TTS |

For a demo video, a prototype, or an experiment with tone, AI Studio is the fastest route
from a sentence to audio you can listen to — and it costs nothing to find out whether the
read works.

## That's the tour

You've been through every category. To start again, pick another from the
[overview](/docs/).
