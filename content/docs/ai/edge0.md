---
weight: 5046
title: "edge0"
description: "The same SSD-streaming trick as Slotstream, aimed the other way — a 35B model held to roughly 1–3 GB of live memory, with a router that prefetches experts before they're needed."
icon: "sd_storage"
date: "2026-09-14"
lastmod: "2026-09-14"
draft: false
---

[Slotstream](/docs/ai/slotstream/) uses SSD streaming to run an enormous model on a
large Mac. [edge0](https://github.com/Edge0-AI/edge0) points the same idea at the
opposite end: hold a **35B model to phone-class memory**. Measured peak active memory is
**2.9 GiB for the 35B tier and 1.0 GiB for the 8B** — not the 20+ GB the parameter count
implies.

The arithmetic that makes it possible: both checkpoints are sparse mixture-of-experts.
The 35B has 256 experts per layer and routes each token through four of them, so about 3B
parameters are active per forward pass. Keep the 4-bit checkpoint memory-mapped on
storage, read only the routed experts, and **peak memory is bounded by the active set
rather than the parameter count.**

## Two things it adds to plain streaming

**Prerouter.** A trained head predicts routing one step ahead, so expert reads overlap the
forward pass instead of stalling it — worth **up to +59% decode throughput**, and the
gain grows the slower your storage is. This is the direct answer to "doesn't reading from
disk make it slow": yes, and the fix is to stop waiting until you know what to read.

**Recover-LoRA.** The int4 base is frozen and LoRA adapters are distilled from the FP
teacher to claw back quantisation loss. The adapters stay unmerged, so one read-only base
serves several adapter sets — swap adapters without touching the 23 GB checkpoint.

## What it costs in quality and speed

On the project's own OpenCompass runs, against the fp16 base models under identical
settings:

| | edge0-35b (int4) | fp16 base | edge0-8b (int4) | fp16 base |
|---|---:|---:|---:|---:|
| HumanEval | 90.9 | 95.1 | 91.5 | 92.7 |
| GPQA-Diamond | 79.8 | 81.8 | 70.7 | 71.2 |
| MMLU-Pro | 81.0 | 84.6 | **70.1** | 65.8 |
| **Average** | **79.2** | 83.2 | **69.9** | 72.7 |

Down 3.9 points on average for the 35B and 2.8 for the 8B — with 8B MMLU-Pro landing
*above* its fp16 base, which is the sort of result to treat as noise rather than magic.
These are vendor-run numbers; they're specific and reproducible, which is better than most,
but they aren't independent.

| Tier | Decode | Prefill (cold / warm) | Peak memory | Disk |
|---|---|---|---|---|
| `edge0-35b` | 14.9–17.7 tok/s | 113 / 140 tok/s | 2.9 GiB | ~23 GB |
| `edge0-8b` | 23.9–25.3 tok/s | 500 / 1428 tok/s | 1.0 GiB | ~4.2 GB |

Measured on a Mac mini M4 Pro with 24 GB. **Cold versus warm is the storage story in one
row**: the first request faults experts in off the SSD, later ones hit the page cache, and
on the 8B that's a 2.8× difference in prefill. Your storage latency is a first-class
performance parameter here, not an implementation detail.

Note also which number is *not* small: **disk**. RAM stops being the constraint and 23 GB
of free storage becomes one — which on a phone is the harder ask.

## Running it

Apple Silicon, macOS, Python 3.10+ (3.12 recommended):

```bash
python3.12 -m venv .venv && .venv/bin/pip install -e '.[dev,fetch]'
.venv/bin/python scripts/fetch_models.py --tier edge0-8b --target-dir models

edge0 demo models/edge0-8b
edge0 serve models/edge0-8b     # OpenAI-compatible /v1/chat/completions
edge0 chat models/edge0-8b --prompt "Explain streaming inference in one sentence."
```

Each Hugging Face repo bundles the base checkpoint with its trained LoRA and prerouter
adapters in one directory, so a single download runs the full pipeline — the tier is
detected from `config.json`. `--no-prerouter` / `--no-lora` fall back to the plain base
model, which is also the way to see what those two pieces are actually buying you.

Start with the **8B**: 4.2 GB instead of 23, and it tells you whether the approach suits
your machine before you commit an evening to the download.

There's an `AutoEngine.from_pretrained()` Python API alongside the CLI. Apache-2.0,
vendored code included.

## About "on a phone"

The headline everywhere is a 35B model on an iPhone, and the memory figures make that
plausible. Be precise about what's supported, though: the README's requirements say the
MLX backend runs on **macOS with Apple Silicon**, with CUDA on the roadmap and no other
platform supported yet. All published benchmarks are from a Mac mini.

iPhone use is real but rougher — there's a known issue where A18 and A18 Pro produce
garbled, mixed-language output unless you pin `mlx==0.30.6` and `mlx-metal==0.30.6`. Treat
the phone as the demonstration and the Mac as the supported target, at least at preview.

## Next

A model on a port still needs somewhere to talk to it →
[Open WebUI](/docs/ai/open-webui/)
