---
weight: 5042
title: "llmfit"
description: "One command that reads your RAM, GPU, and VRAM and tells you which local models will actually run — before you download 20 GB to find out."
icon: "straighten"
date: "2026-09-13"
lastmod: "2026-09-13"
draft: false
---

The first hour with [Ollama](/docs/ai/ollama/) usually goes: pull a model that sounds
good, wait, watch it crawl at two tokens a second or refuse to load at all, pull a smaller
one, repeat. [llmfit](https://github.com/AlexsJones/llmfit) skips the loop. It inspects
the machine — CPU, RAM, discrete or unified GPU memory, CUDA/Metal/ROCm — and scores
hundreds of catalogued models on whether they fit, how fast they'd run, and how good
they are, then picks the best quantization that fits rather than assuming one.

Rust, MIT, single binary.

## Install

```bash
brew install llmfit                                   # macOS / Linux
curl -fsSL https://llmfit.axjns.dev/install.sh | sh   # anywhere
uv tool install -U llmfit                             # if you live in Python
docker run -it --rm ghcr.io/alexsjones/llmfit --tui   # no install
```

## Use

```bash
llmfit                       # TUI: browse, filter with /, sort, read the fit score
llmfit recommend             # hardware summary + the short list, as text
llmfit recommend --json      # same, for scripts
llmfit fit                   # every model in the catalog, ranked by fit
llmfit info "qwen2.5-coder:14b"   # why this one does or doesn't fit
llmfit doctor                # what it detected — check this first on odd hardware
llmfit bench                 # measure real tokens/s against a running Ollama/llama.cpp
```

The four scores are **fit** (does it load in memory), **speed** (a bandwidth model
calibrated on community measurements, then corrected by `bench` if you run it),
**quality**, and **context**. The quantization ladder walks Q8_0 → Q6 → Q5 → Q4 → Q2 and
stops at the best one that fits, so a 14B model on a 16 GB Mac comes back as "yes, at
Q4_K_M, ~18 tok/s" instead of "no."

It knows Ollama, llama.cpp, MLX, LM Studio, and Docker Model Runner as providers, and
GGUF/AWQ/GPTQ/EXL2 as formats, so the recommendation is something you can paste into
`ollama pull` rather than a Hugging Face name to go translate.

## The honest ceiling

It's a catalog plus a bandwidth model. New models take a release to show up, and the
speed number is an estimate until `bench` replaces it with a measurement. Treat the
output as a shortlist of three, not a verdict — then pull the top one and run `bench`.

## Next

Run the model it picked → [Ollama](/docs/ai/ollama/)
