---
weight: 5045
title: "Slotstream"
description: "Running a 105 GB mixture-of-experts model on a Mac that doesn't have 105 GB of RAM, by streaming experts from SSD."
icon: "memory"
date: "2026-09-06"
lastmod: "2026-09-06"
draft: false
---

[Ollama](/docs/ai/ollama/) draws a hard line: if the model doesn't fit in memory, you
don't run it. Slotstream moves that line. It runs Qwen3.8-Flash-Next — 125 billion
parameters, 105 GB of weights — on a 48 GB Mac by keeping most of the model on SSD and
pulling in only the pieces each token needs.

## Whether your Mac can do it

Apple Silicon, macOS 14 or later, and about **110 GB of free SSD**. The program is
small; the model is not.

```bash
curl -fsSL https://raw.githubusercontent.com/carloslfu/slotstream/main/install.sh | sh
slotstream doctor
```

`doctor` prints the memory plan, an estimated speed, and free disk — before downloading
anything. Run it first.

| Mac RAM | Estimated speed |
|---|---|
| 8 GB | ~3 tok/s, needs swap and slows the whole machine |
| 16 GB | ~4 tok/s estimated — one measured 16 GB M2 mini managed 1.41 |
| 24–32 GB | ~8–9 tok/s |
| 48 GB and up | ~12 tok/s on an M5 Pro |

Below 24 GB this is a curiosity rather than a tool. The gap between the 16 GB estimate
and that measured 1.41 tok/s is the honest number to plan around: SSD speed matters as
much as RAM.

## First run

```bash
slotstream pull          # 88.3 GB compressed, resumes, hash-checked, no HF account
slotstream run --prompt "Why is the sky blue?"
```

The download is a couple of hours on 100 Mbps and once only. After that it's offline.

## As a server

It speaks Ollama's API and a subset of OpenAI's, on the port Ollama uses — so existing
clients point at it unchanged.

```bash
slotstream serve
```

```bash
curl localhost:11434/api/chat -d '{
  "model": "qwen3.8-flash-next:4bit",
  "messages": [{"role": "user", "content": "Hello"}]
}'
```

Open WebUI takes `http://localhost:11434` as an Ollama server; an OpenAI SDK takes
`http://localhost:11434/v1` with any string as the key. Images work on all three APIs
(base64 or a `data:` URL — it won't fetch a web URL for you):

```bash
slotstream run --image cat.jpg --prompt "What is in this picture?"
```

Tool calling is OpenAI-endpoint only; the Ollama endpoints reject tools, and
JSON-schema output and logprobs return a 400 everywhere.

## What it costs you in patience

Generation speed isn't the number that will surprise you — **prefill** is. Processing a
long prompt before the first token takes roughly 9 s for 2,000 tokens and 39 s for
8,000 on the fast configuration, and minutes as you approach the default 32,768-token
context. Follow-up turns reuse unchanged history, which is what makes conversation
bearable: one measured eight-turn test started replying in 6.0 s with reuse against
25.8 s without.

```bash
slotstream serve --max-context 65536    # the larger window
slotstream serve --memory-gb 16         # pin the target instead of auto
```

Left alone, it picks a memory target, then checks pressure every 15 seconds and resizes
its expert cache between requests — giving memory back when you open something else.

## How it works

A mixture-of-experts model uses only a few of its expert networks per token. Of
Qwen3.8-Flash-Next's storage, 3.8 GB is shared weights that stay in RAM; the rest is
68 GB of routed experts and a 32 GB lookup table on disk. Slotstream keeps a fixed pool
of cache slots that all 48 layers share, and reads experts into those slots as the
routing demands — fewer and larger disk reads than simply memory-mapping the file.

## Where it fits

| Use Slotstream when | Use Ollama when |
|---|---|
| You want one frontier-size model locally, and can wait | You want any of hundreds of models, now |
| The Mac has 32 GB+ and a fast SSD | The machine is modest, or isn't a Mac |
| Batch or conversational work, offline | Latency matters, or something calls it in a loop |

It's a Swift binary with no Python, MIT-licensed, and single-model on purpose. Linux
and Windows are stated plans, not shipped.

## Next

A model on a port still needs somewhere to talk to it →
[Open WebUI](/docs/ai/open-webui/)
