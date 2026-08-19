---
weight: 5040
title: "Ollama"
description: "Running models on your own machine, and what to realistically expect from them."
icon: "dns"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

When policy forbids sending code outside, when things must work offline, or when the
cost of high-volume requests matters, a local model becomes an option. Ollama reduces
that to roughly the effort of `docker run`.

## Install

```bash
brew install ollama
ollama serve          # start the background service
```

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Windows uses the official installer.

## Running a model

```bash
ollama pull llama3.2          # download
ollama run llama3.2           # interactive
ollama list                   # installed models
ollama rm llama3.2            # remove
```

You can also pipe a prompt in:

```bash
cat error.log | ollama run llama3.2 "Find the root cause in this log"
```

## Choosing a model

| Parameter size | Rough memory | Use |
|---|---|---|
| 1–3B | 4 GB | Summaries, classification, simple transforms |
| 7–8B | 8–16 GB | General questions, explaining code |
| 13–14B | 16–32 GB | Somewhat harder reasoning |
| 30B+ | 32 GB and up | Quality first, speed second |

Quantised builds (`:q4_K_M` and friends) trade some quality for memory. On a laptop, a
quantised 7–8B model is the realistic starting point.

## Wiring it into code

Ollama exposes a local HTTP server.

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.2",
  "prompt": "Three common reasons a SELECT does not use an index",
  "stream": false
}'
```

There is an OpenAI-compatible endpoint too, so existing SDK code often works with only
a base URL change.

```bash
curl http://localhost:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"llama3.2","messages":[{"role":"user","content":"hello"}]}'
```

## Defining a custom model

A `Modelfile` pins the system prompt and parameters.

```
FROM llama3.2
PARAMETER temperature 0.2
SYSTEM """
You are a code reviewer. Answer concisely and give at most three findings, each with
a reason.
"""
```

```bash
ollama create reviewer -f Modelfile
ollama run reviewer
```

## Realistic expectations

- **Quality is below the hosted commercial models.** Complex refactors and long-context
  reasoning are still out of reach.
- **Good fits**: log summarisation, classification, translation drafts, format
  conversion, first-draft commit messages.
- **Speed**: you want a GPU or Apple Silicon unified memory. CPU-only is painfully slow.
- **Disk**: models run to several gigabytes each. Prune with `ollama list` from time to
  time.

## Next

Changes made with AI need version control around them →
[Git & Collaboration](/docs/git/)
