---
weight: 12070
title: "OpenWhispr"
description: "Dictation into any app from a hotkey, with the audio staying on your machine if you want it to."
icon: "mic"
date: "2026-09-07"
lastmod: "2026-09-07"
draft: false
---

The rest of this category turns text into media. OpenWhispr runs the other way: hold a
hotkey, talk, and the words land at your cursor — in the editor, the browser, the commit
message, wherever the caret happens to be. It's the open-source answer to Wispr Flow and
Granola, and unlike them it can run entirely offline.

## Install

Download a build from the [releases page](https://github.com/OpenWhispr/openwhispr/releases/latest)
— `.dmg` for macOS, `.exe` for Windows, `.AppImage`, `.deb`, `.rpm`, or `.tar.gz` for
Linux. It's MIT-licensed, free, and collects nothing.

The default hotkey is backtick (`` ` ``); change it if you write a lot of Markdown code
fences.

## Local or cloud, per feature

This is the choice that matters. Transcription, AI processing, speaker labelling, and
note search each work with a local model or a cloud provider.

| | Local | Cloud |
|---|---|---|
| Transcription | Whisper via whisper.cpp, or NVIDIA Parakeet — GPU-accelerated on Metal, CUDA, and Vulkan | OpenAI and other providers, faster on a slow machine |
| AI processing | A local LLM via llama.cpp | GPT-5, Claude, Gemini, Groq, OpenRouter — your own key |
| Speaker labelling | On-device diarization with voice fingerprints | — |

Pick local and the audio never leaves the machine, which is the difference between
"handy" and "allowed" for anything under NDA. Whisper models come in the usual sizes
(tiny through large and turbo); start with `base` or `small` and move up only if the
accuracy annoys you.

## What it does beyond dictation

- **Dictate in one language, paste in another** — a separate hotkey for translation.
- **Voice assistant hotkey** — what you say goes straight to the AI as a command rather
  than as text. Highlighted text gets edited in place; you can opt into sending a
  screenshot as context.
- **Meeting transcription** — detects Zoom, Teams, and FaceTime calls, labels speakers
  live, and hooks into Google, Microsoft, or Apple Calendar.
- **Audio import** — drag in files, batch-upload, or paste a YouTube or audio URL.
- **Notes** — folders, semantic search, AI actions, optional cloud sync and sharing.
- **API and MCP server** — reach transcriptions and notes from your own code, or wire
  the whole thing into an agent.

## Practical notes

- **Intel Macs lose live speaker identification and voice fingerprinting** — ONNX
  Runtime stopped shipping macOS x86_64 binaries. Meetings still record and transcribe;
  note search falls back to keywords.
- **Recording other people is a consent question, not a feature question.** Rules on
  recording a call differ by jurisdiction and by employer. Ask first.
- **Dictation is faster than typing and worse at names.** Proofread anything going into
  a commit, a ticket, or a customer's inbox.

## Running from source

```bash
git clone https://github.com/OpenWhispr/openwhispr.git
cd openwhispr
npm install
npm run dev        # Node.js 24+
```

Electron, React, TypeScript, whisper.cpp, and sherpa-onnx underneath.

## That's the tour

You've been through every category. To start again, pick another from the
[overview](/docs/).
