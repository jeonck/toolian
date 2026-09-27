---
weight: 150750
title: "VoiceStudio"
description: "A desktop app that wraps local speech models into real workflows — voice cloning, dubbing, dictation, transcription, and audiobooks, with a local API and an MCP server."
icon: "graphic_eq"
date: "2026-09-27"
lastmod: "2026-09-27"
draft: false
---

[OmniVoice](/docs/ai-media/omnivoice/) is a model you call from Python.
[VoiceStudio](https://github.com/debpalash/VoiceStudio) is the application built around
it — and around whichever other engine you pick. Same local-only premise, but with the
workflows attached: clone a voice, dub a video with timed speech, dictate into whatever
window has focus, transcribe, batch a whole audiobook.

It bills itself as the open-source ElevenLabs alternative, and the comparison is fair on
capability. AGPL-3.0, Electron desktop app, 646 languages, nothing leaving the machine
unless you opt into a remote worker.

## Install

```sh
curl -fsSL https://voicestudio.sh/install | sh          # latest release
curl -fsSL https://voicestudio.sh/install | sh -s -- --version X.Y.Z
curl -fsSL https://voicestudio.sh/install | sh -s -- --uninstall   # keeps your data
```

macOS and Linux; Windows and Docker have their own guides, and there are plain
[release downloads](https://github.com/debpalash/VoiceStudio/releases/latest). The
installer preserves settings, projects, and models across upgrades.

Then: open **Voice cloning**, pick a bundled voice or drop in a clean reference
recording, type your text, generate. It prompts for the model download when it needs one.

There's also an agent path, which is a neat touch — hand your coding agent the
[agent guide](https://github.com/debpalash/VoiceStudio/blob/main/docs/install/agent.md)
and it handles hardware detection, reuses existing data, and asks before pulling models:

```text
Install the VoiceStudio Electron app on this device and verify it works, following
https://github.com/debpalash/VoiceStudio/blob/main/docs/install/agent.md
```

## What it actually does

| Workspace | For |
|---|---|
| Voice cloning | A voice from a few seconds of reference audio |
| Voice design | Describe the voice you want in words, no reference needed |
| Video dubbing | Speech timed to an existing video track |
| Dictation | A floating widget — talk, words land at the cursor |
| Transcription | The other direction, locally |
| Stories & audiobooks | Long-form batch jobs rather than one clip at a time |
| Models | Install, switch, and manage engines from the UI |

The default engine is [OmniVoice](/docs/ai-media/omnivoice/) (k2-fsa), with others
selectable per job — so the model page and this page are the same stack at two altitudes.

## Local API and MCP

The part that makes it more than a nice UI:

- A **local HTTP API**, so your own scripts can generate speech without a cloud key.
- An **MCP server**, so an agent can produce audio as a step in a larger job — narrate a
  generated video, read back a draft, dub a clip — without you in the loop.
- **Agent skills** via `npx skills add debpalash/VoiceStudio` (`voicestudio` for audio
  work, `voicestudio-maintainer` for repo maintenance).

That combination is the argument for it over a hosted service: the per-character meter
disappears, and "generate 400 lines of narration" stops being a budgeting decision.

## Where it sits

| | ElevenLabs | VoiceStudio |
|---|---|---|
| Runs | Hosted | Your machine |
| Cost | Per character | Electricity |
| Ceiling | Best-in-class quality, quickly | Depends on your GPU and chosen engine |
| Data | Leaves your machine | Doesn't |
| Setup | An API key | An install and a model download |

Reach for [ElevenLabs](/docs/ai-media/elevenlabs/) when quality is the whole point and
volume is low. Reach for VoiceStudio when volume is high, the audio is sensitive, you're
offline, or you want an agent to make audio unattended.

## Before you rely on it

- **AGPL-3.0**, and — stated separately in the README — **the models carry their own
  licences**. Check both before anything commercial; an Apache-2.0 model inside an AGPL
  app is still two sets of terms.
- **Hardware decides the experience.** Requirements vary by engine; the project publishes
  [benchmarks](https://github.com/debpalash/VoiceStudio/blob/main/docs/benchmarks.md)
  and a performance guide. Read them before planning a big batch.
- **Tauri is gone.** 0.5.3 was the last Tauri release; Electron is now the only desktop
  app and web UI, and old installs need a separate migration.
- **Clone voices only with permission.** The project says this plainly and so should
  anyone using it. A convincing clone of someone who didn't agree is a problem no licence
  fixes.

## Next

The other direction — turning what you say into text →
[OpenWhispr](/docs/ai-media/openwhispr/)
