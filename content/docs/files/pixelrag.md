---
weight: 3100
title: "PixelRAG"
description: "Berkeley's visual RAG: index documents as page screenshots instead of parsed text, so tables and charts survive retrieval — plus a plugin that lets Claude look at a page."
icon: "screenshot_monitor"
date: "2026-09-11"
lastmod: "2026-09-11"
draft: false
---

Every converter on the pages before this one has the same failure mode. A page is parsed
to text, the table becomes a run-on paragraph, the chart becomes nothing at all — and the
retrieval step that follows can't find an answer that was clearly visible in the original.

[PixelRAG](https://github.com/StarTrail-org/PixelRAG) declines the parse.
Documents are **rendered to screenshot tiles**, the tiles are embedded and indexed, and
retrieval happens over images. The reader model reads the number off the picture the way
you would. It's the code behind [a Berkeley paper](https://arxiv.org/abs/2606.28344) out
of SkyLab, BAIR and Berkeley NLP, Apache-2.0 licensed.

Two pieces make it work: rendering instead of parsing, and a `Qwen3-VL-Embedding` model
LoRA-fine-tuned on screenshot data so that page images land in a space where visual
content is actually retrievable.

## Try it without installing anything

A hosted index of **8.28M Wikipedia pages** answers on a public endpoint — no key, no
setup:

```bash
curl -X POST https://api.pixelrag.ai/search \
  -H "Content-Type: application/json" \
  -d '{"queries": [{"text": "What is the capital of France?"}], "n_docs": 5}'
```

It accepts an image as the query too, which is the part worth playing with — search by
what a thing *looks like*. There's a browser demo at [pixelrag.ai](https://pixelrag.ai)
and a Colab quickstart if you'd rather see the tiles inline.

## The pipeline

Capture is a standalone command; everything else hangs off the `pixelrag` umbrella, and
you install only the stages you need:

| Command | What it does | Install |
|---|---|---|
| `pixelshot` | Document → image tiles (Playwright CDP, PDF) | `pip install pixelrag` |
| `pixelrag chunk` · `embed` · `build-index` | Tiles → vectors → FAISS index | `pip install 'pixelrag[embed]'` |
| `pixelrag index` | The whole thing: source → ingest → embed → index | `pip install 'pixelrag[index]'` |
| `pixelrag serve` | FAISS search API over FastAPI, CPU or GPU | `pip install 'pixelrag[serve]'` |

## Indexing your own documents

Runs on Linux with CUDA and on Apple Silicon via MPS — `device: auto` picks the backend,
and no GPU is strictly required:

```bash
pip install 'pixelrag[index,pdf]'      # PDFs also need poppler: brew install poppler

cat > pixelrag.yaml << 'EOF'
source:
  type: local
  path: ./my_docs

embed:
  model: Qwen/Qwen3-VL-Embedding-2B
  device: auto

output: ./my_index
EOF

pixelrag index build
pixelrag serve --index-dir ./my_index --port 30001
```

Then POST to `http://localhost:30001/search` with the same body as the hosted endpoint.

**Before you reach for the pre-built Wikipedia index**: the base FAISS index on Hugging
Face is around **217 GB**. That's the honest cost of indexing 8.28M pages as pixels, and
the reason the hosted API exists. Check your disk before `huggingface-cli download`, and
use `--include` to take one index rather than all four.

A separate note if you plan to fine-tune: `train/` is its **own uv project** with a
pinned environment (`torch==2.9.1+cu129`, `transformers==4.57.1`, cuDNN 9.20). Install it
from inside that directory, never from the repo root.

## Giving Claude eyes

The renderer ships as a Claude Code plugin — `pixelbrowse`. Instead of fetching raw HTML,
Claude screenshots the page and reads the image, so charts, diagrams and layout arrive
intact:

```bash
uv tool install pixelrag          # or: pipx install pixelrag
claude plugin marketplace add StarTrail-org/PixelRAG
claude plugin install pixelbrowse@pixelrag-plugins
```

Then `/screenshot https://example.com` in a session, or ask in passing — "screenshot
this arxiv page and explain the findings". No MCP server and no backend: the skill shells
out to `pixelshot` on your machine.

**Use `uv tool` or `pipx`, not a plain `pip install`.** Installing into a project
virtualenv leaves `pixelshot` off your `PATH`, and the skill then fails for a reason that
looks nothing like the cause. opencode users get the same tool through the
`@startrail/pixelbrowse` npm package.

## When to reach for which

| Situation | Reach for |
|---|---|
| Prose-heavy PDFs, you want editable Markdown | [MinerU](/docs/files/mineru/), [Docling](/docs/files/docling/) |
| Many formats, fast, feeding a text pipeline | [MarkItDown](/docs/files/markitdown/) |
| The answer lives in a table, chart, or layout | PixelRAG |

The dividing question isn't which parser is best. It's whether the thing you need to
retrieve survives being turned into text at all — and for financial tables, scanned
forms, dashboards and infographics, it frequently doesn't.

## Next

With files under control, on to where you write the code →
[Editors & IDEs](/docs/editor/)
