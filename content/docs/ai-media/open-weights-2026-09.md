---
weight: 12095
title: "Open-Weight Picks"
description: "The same question as the best-in-class snapshot, restricted to models you can download — where the licence matters more than the benchmark, and half the names in circulation are wrong."
icon: "lock_open"
date: "2026-09-23"
lastmod: "2026-09-23"
draft: false
---

[Best in Class](/docs/ai-media/best-in-class-2026-09/) answers "what's the best model for
this job". This page answers a narrower one: **what's the best model I can download, run,
and ship a product on.** Dated 23 September 2026, and stale the moment it's published.

One rule governs everything below. With hosted models you read the pricing page; with
open weights **you read the licence, and the licence is the spec.** Apache-2.0 and MIT
mean do what you like. Anything else — non-commercial, source-available, research-only —
is a constraint on your product, not a footnote.

| Task | Pick | Licence | Needs |
|---|---|---|---|
| Cut-outs, background removal | **BiRefNet** | MIT | Modest GPU; native up to 2048² |
| Segment / erase by description | **SAM 3.1** (Meta) | See repo | GPU |
| Speech recognition | **SenseVoice** (Alibaba) | Apache-2.0 | CPU-viable |
| Speech synthesis | **CosyVoice 3** (Alibaba) | Apache-2.0 | 0.5B — small |
| Music | **ACE-Step 1.5 XL** | MIT | <10 s/song on a 3090 |
| Image → 3D | **TRELLIS.2** (Microsoft) | MIT | **24 GB VRAM** |
| Image generation, quality | **FLUX.2 [dev]** | Non-commercial | 4090 at 4-bit |
| Image generation, commercial | **Qwen-Image** / **FLUX.2 [klein]** | Apache-2.0 | Single GPU |
| Video generation | **Wan** (Alibaba) | Apache-2.0 | 24 GB for the big tier |
| Document OCR | **PaddleOCR-VL-1.6** | See repo | **Runs on CPU** |
| OCR, ultra-light | **GLM-OCR** (Z.ai) | See repo | 0.9B — edge-deployable |
| Coding | **Qwen3.8-27B** / **GLM-5.3** | Apache-2.0 (Qwen) | 27B at 4-bit on one card |

## The licence traps

**FLUX.2 [dev] is the one people get wrong**, and the distinction is worth stating
precisely because it cuts the other way from what you'd assume:

> The **weights** are under the FLUX [dev] Non-Commercial License — non-commercial,
> non-production use only. The **images you generate** with them may be used personally,
> scientifically **and commercially.**

So you can sell the picture; you cannot build the service. If you need the model inside a
product, [Black Forest Labs' own](https://bfl.ai/) `FLUX.2 [klein]` (4B) is Apache-2.0, as
is Alibaba's Qwen-Image — which is also the better pick when the image has to contain
legible text.

**"MIT" on BiRefNet means the licence, not the institution.** BiRefNet comes from Peng
Zheng and co-authors (CAAI AIR'24), not from MIT the university. The confusion is
everywhere in Korean-language roundups, and it matters only because it sends you looking
for the wrong paper.

## Names that don't survive a check

The list circulating in video roundups has several transcription casualties. If you're
searching for these, search for the second column:

| Circulating as | Actually |
|---|---|
| "MetaSeg 3.1" | **SAM 3.1** — Meta's Segment Anything. Text prompts arrived in SAM 3 |
| "TRELLIS 2 (3B)" | **TRELLIS.2**, and it's **4B** (`microsoft/TRELLIS.2-4B`) |
| "GLM-OCR 0.9, a 9B model" | **GLM-OCR** *is* the 0.9B model — the 0.9 is the size |
| "Imigen" | No model by that name checks out. The Apache-2.0 slot it describes is filled by Qwen-Image or FLUX.2 [klein] |

## Two claims worth tempering

**SenseVoice is not straightforwardly the best open ASR for Korean.** Apache-2.0 and
genuinely fast — 50–100× real time — and it does support Korean. But its documented
strength is Mandarin and Cantonese, and independent comparisons have **Whisper Large-v3
ahead of it on Japanese and Korean**. If Korean is your primary language, benchmark both
on your own audio before committing. For Korean TTS, CosyVoice 3 is the safer
recommendation: 0.5B, Apache-2.0, Korean among its nine languages, voice cloning from
3–10 seconds.

**"Best open coding model" is contested, not settled.** As of September the live
open-weight composite has **Qwen3.8 Max first and GLM-5.3 second**, while Kimi K3 leads
the frontend code arena. GLM-5.3 does lead on terminal and agentic benchmarks, which may
well be the axis you care about — but the flat claim that it tops the coding board
doesn't hold. For running locally on one card, **Qwen3.8-27B** is the practical answer:
dense, Apache-2.0, and the smallest footprint at the top of the self-hosting tables.

## The two sizes worth internalising

Two numbers decide most of this list for a home machine:

- **24 GB VRAM** is the gate. TRELLIS.2 and the large Wan tiers sit behind it; a 4090 or
  better is the entry ticket. Below that, quantisation and offload get you into the room —
  Wan drops to 6–8 GB at 480p with GGUF and CPU offload — at a cost in speed.
- **Under 1B parameters** is where the pleasant surprises are. GLM-OCR at 0.9B and
  CosyVoice 3 at 0.5B both compete with far larger models, and OCR is the one area where
  open models genuinely beat the paid ones — see
  [PaddleOCR-VL](/docs/ai-media/best-in-class-2026-09/) at 96.3 on OmniDocBench, on a CPU.

For running any of these locally, start at [llmfit](/docs/ai/llmfit/) to size the model to
your hardware, and [Ollama](/docs/ai/ollama/) to serve it.

## Next

Services worth running on your own hardware → [Self-Hosted](/docs/self-hosted/)
