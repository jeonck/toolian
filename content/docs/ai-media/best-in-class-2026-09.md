---
weight: 12090
title: "Best in Class, September 2026"
description: "Which model actually leads each media task right now — transcription, music, 3D, speech, OCR, image, video, code — and where the popular answer is a version behind."
icon: "leaderboard"
date: "2026-09-09"
lastmod: "2026-09-09"
draft: false
---

Every page in this category picks a tool and sticks with it, because tools have habits
worth learning. **Models** don't work that way — the leader changes monthly, and half the
names circulating in roundup videos are a version behind or slightly wrong.

This is a snapshot dated **8 September 2026**, with the leaderboard and the caveat for
each task. Treat it the way you'd treat a stock quote.

| Task | Current pick | Why |
|---|---|---|
| Realtime transcription | ElevenLabs **Scribe v2 Realtime** | ~150 ms end-to-end, 90+ languages |
| Transcription accuracy | Microsoft **MAI-Transcribe-1** | 3.8% avg WER on FLEURS across 25 languages |
| Music (quality) | **Suno v5.5** | Lyrics in, vocals and backing out — but see the licence note |
| Music (safe to sell) | **ElevenLabs Music v2** | Trained only on licensed catalogue |
| 3D assets | **Meshy** (v7) | Cleanest production-ready meshes; **Rodin** wins raw geometry |
| Speech synthesis | **Cartesia Sonic-3.6** | #1 on both Artificial Analysis speech arenas |
| Korean expressiveness | ElevenLabs **Eleven v3** | Audio tags and delivery direction, 70+ languages |
| Document OCR | **PaddleOCR-VL-1.6** | 96.3 on OmniDocBench v1.6, at ~1B params, on CPU |
| Image generation | **GPT Image 2** | Arena Elo 1512 — a 241-point gap, and text that spells |
| Text→video | **Gemini Omni Flash** | #1 text-to-video, with synchronised audio in one pass |
| Image→video | **MiniMax H3** | #1 among open-weight models — read the fine print |
| Coding (benchmarks) | **Claude Fable 5.1** | Leads the automated boards |
| Coding (user votes) | **GPT-6 Astra** | #1 on Code Arena: WebDev at 1,797 |

## Transcription

Two different jobs, two different winners. For **live** captions — calls, meetings,
agents that have to answer while you're still talking — ElevenLabs' realtime model runs
around 150 ms end-to-end across 90+ languages, with diarisation and word-level stamps.

For **accuracy on a finished recording**, Microsoft's MAI-Transcribe-1 (April 2026, the
first proprietary speech model out of the MAI group) posts 3.8% average WER on FLEURS
across 25 languages and beats Whisper Large v3 on every one of them, in Azure AI Foundry
at roughly half the GPU cost.

> The name doing the rounds is "Scribe v3". There isn't one. The current ElevenLabs
> speech-to-text line is **Scribe v2**, with **Scribe v2 Realtime** as the streaming
> variant — v3 is their *text-to-speech* model, which is a different product entirely.
> Easy to conflate, and it'll send you to the wrong API docs.

## Music, and the licence that matters more than the audio

Suno v5.5 (March 2026) is the better-sounding model: hand it lyrics and it returns
vocals, arrangement, and mix in one pass.

The catch isn't quality, it's provenance. In **July 2026 the Munich Regional Court ruled**
that Suno's models had memorised copyrighted works in training and that those
compositions were recognisable in the output — Europe's first significant judgement that
training on copyrighted music needs authorisation. Suno has said it's building
licence-only models to replace the current ones. Until those land, think hard before a
Suno track goes into anything commercial with European exposure.

**ElevenLabs Music v2** is the answer to that specific problem: trained only on licensed
catalogue via Merlin and Kobalt deals, with artists opting in, and every generated track
cleared for commercial use. The self-serve tiers cover marketing video, YouTube,
podcasts, and ads — film, TV, and Studio Games need Enterprise. Slightly less impressive
output, considerably better sleep.

## 3D

Meshy is the practical default: clean topology, strong texturing, and exports that land
in Blender, Unity, and Unreal without a fight. Its conversational 3D Agent lets you
iterate by asking rather than re-rolling, and textures go to 8K.

**Rodin** beats it on raw geometric detail — it displaces geometry from the texture
instead of smoothing everything into a soft blob — at the cost of heavy meshes you'll
retopologise before any engine sees them. Detail-first work: Rodin. Anything headed for a
game or a deadline: Meshy.

> Roundups still say "Meshy 6". **Meshy 7 shipped in August 2026.** Meshy 6 hit stable in
> January, so the recommendation is right and the version number is two releases stale.

## Speech synthesis

Cartesia's **Sonic-3.6** (August 2026) holds #1 on *both* Artificial Analysis speech
arenas — Controlled Voice at 1,144 Elo, Provider Voice at 1,286 — ahead of Speechify's
Simba 3.2 and Alibaba's Qwen-Audio-3.0-TTS-Plus. It's a streaming model, so the ranking
comes without the usual latency trade.

For Korean specifically, **Eleven v3** is still the more expressive read: audio tags let
you direct emotion, pauses, and multi-speaker dialogue rather than nudging sliders. It
sits third on Controlled Voice at 1,060 — arena Elo and "sounds right in Korean" are not
the same measurement, and this is where that gap shows.

## OCR

**PaddleOCR-VL-1.6** (Baidu, May 2026) scores 96.3 on OmniDocBench v1.6 and pulls tables
and equations out with their structure intact rather than as flattened text. The headline
isn't the score, it's the size: roughly 1B parameters, built on ERNIE-4.5-0.3B, beating
models hundreds of times larger — and **it runs on a CPU**, which makes it the rare
document model you can put next to the documents instead of shipping them to an API.

Worth pairing with [MinerU and Docling](/docs/files/) if you're building a pipeline
rather than reading one PDF.

## Image

**GPT Image 2** (April 2026) took the Text-to-Image Arena at 1,512 Elo — a 241-point lead,
the largest margin in that board's history. The practical difference from every model
before it is **text inside the image**: menus, signs, posters, and labels come out
correctly spelled, including dense Korean, Japanese, and Chinese. That single capability
retires a lot of "generate the background, add the type in Figma" workflows.

For the free, no-account option that's still fine for most drafts, see
[Raphael](/docs/ai-media/raphael/).

## Video

**Text→video**: Gemini Omni Flash leads, and generates matching audio — effects,
narration, music — synchronised in the same pass rather than as a second job. Google
shipped Omni 1.1 Flash in late August and it retook #1 the next day.

**Image→video**: MiniMax H3 (Hailuo 3.0) is the one people mean, and the "#1" needs
qualifying. It leads **open-weight** image-to-video with audio, and tops the Video Edit
Arena at 1,390. On the overall image-to-video board it sits around third, behind Gemini
Omni Flash and Wan 3.0. Still the best thing you can run yourself from a single
photograph — just not the outright leader the shorthand suggests.

## Coding

The two coding leaders disagree because they measure different things, and that's the
useful part.

**Automated benchmarks** favour Claude Fable 5.1 (September 2026). One caveat worth
carrying: the widely-quoted "95% on SWE-bench Verified" is a third-party leaderboard
figure, not a published one — the number Anthropic actually reports, in the system card,
is **81.2 on SWE-bench Pro**. Different benchmark, much harder, still a lead.

**Human votes** favour GPT-6 Astra, which took #1 on Code Arena: WebDev on 5 September at
1,797, thirty-five points over Fable 5.1, at pricing that matches Anthropic's.

Benchmarks reward closing the issue; arena voters reward the app looking right. Pick by
which one your work resembles — and note that neither number describes the agent harness
you'll actually be typing into, which is covered in
[AI Coding Tools](/docs/ai/).

## How to read this page in three months

Don't. Re-check the boards — [Artificial Analysis](https://artificialanalysis.ai/) for
speech, video, and OCR, [Arena](https://arena.ai/) for image, video, and code — and
assume every version number above has moved. What ages well here isn't the picks, it's
the shape of the questions: realtime or accurate, licensed or merely good, benchmark or
ballot, open weights or best overall.

## Next

Services worth running on your own hardware → [Self-Hosted](/docs/self-hosted/)
