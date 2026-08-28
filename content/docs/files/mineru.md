---
weight: 3070
title: "MinerU"
description: "Turning PDFs, scans, and Office files into LLM-ready Markdown — layout, tables, and formulas kept, not flattened."
icon: "document_scanner"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

`pdftotext` gives you the words in roughly the wrong order. Classic OCR gives you the
words in a scan, with the table turned into soup and the equation into punctuation. Both
are fine until you need to *use* the document — feed it to a model, put it in notes, diff
two revisions of a spec — and then the structure you lost was the point.

[MinerU](https://github.com/opendatalab/MinerU) parses the document instead of reading
characters off it: it recovers reading order, keeps tables as tables, formulas as LaTeX,
and figures as images, and writes Markdown and JSON. Version 2.5 moved to a compact
vision-language model — around 1.2B parameters — that scores at the top of the public
document-parsing benchmarks while being small enough to run on a modest GPU, or on CPU
through the pipeline backend.

## Install and run

```bash
pip install uv
uv pip install -U "mineru[all]"

mineru -p report.pdf -o out/
mineru -p ./inbox -o out/ -b pipeline     # CPU-only machines
```

Output lands in `out/` as Markdown, a JSON structure with the blocks and their
coordinates, and the extracted images. The Markdown is what you read; the JSON is what
you build on when you need positions or block types.

Python 3.10–3.13, 16 GB RAM as a floor, and about 20 GB of disk for a full install with
models. There's a Gradio web UI (`mineru-gradio`), a REST API (`mineru-api`), Docker
images, and an MCP server if you want an agent to call it directly.

## Choosing a backend

| Backend | Needs | Use when |
|---|---|---|
| `pipeline` | CPU, or ~4 GB VRAM | Batch work, no GPU, and you want predictable output |
| `hybrid-engine` | 8 GB+ VRAM | Digital PDFs — native text where it exists, the model where it doesn't |
| `vlm-engine` | 8 GB+ VRAM | Scans and hard layouts, highest accuracy |
| `http-client` | Network | Point at a server running the model, keep the client thin |

The pipeline backend is the honest default for a first run: it's fast, stable, and — the
line from the project's own docs worth repeating — it doesn't hallucinate. A VLM
reconstructing a smudged table can produce a *plausible* number, which is a different and
more dangerous failure than an obviously mangled one.

## What it's genuinely good at

- **Academic and technical PDFs.** Multi-column reading order, footnotes, and formulas as
  LaTeX rather than as mangled symbols.
- **Tables.** Structure preserved as HTML or Markdown, including tables that span pages.
- **Scanned documents**, with recognition across 109 languages.
- **Office formats.** DOCX, PPTX, and XLSX go through the same pipeline, so one command
  handles a mixed folder.

The obvious use is feeding a RAG index or an agent: chunking Markdown with real headings
beats chunking a wall of extracted text, and it's the difference between retrieval that
finds the right section and retrieval that finds the right page.

Local processing is the other half of that. The documents worth parsing are often the ones
you can't upload to an API — contracts, patient records, internal specs — and this runs on
your own machine.

## Check the output before you trust it

Every document parser is a probability model wearing a CLI. Two habits:

- **Spot-check the numbers.** Tables and figures are where errors hide, and a wrong digit
  in a Markdown table reads exactly like a right one. Verify anything you'll make a
  decision on against the original page.
- **Keep the source.** Store the PDF alongside the Markdown with the page mapping from the
  JSON, so a surprising claim can be traced back rather than argued about.

## Licence, and the alternatives

MinerU ships under its own licence, based on Apache 2.0 with additional conditions.
That's more permissive than the AGPL it used to carry, and "additional conditions" is
exactly the phrase to read carefully before building a commercial product on it rather
than assuming.

| Need | Tool |
|---|---|
| Structure-preserving parsing, local, many formats | MinerU |
| Simple text out of a digital PDF | `pdftotext`, `pymupdf` |
| Plain OCR of an image, minimal setup | Tesseract, PaddleOCR |
| Make a scanned PDF searchable in place | `ocrmypdf` |
| Markdown from many document types, quickly | [Docling](/docs/files/docling/), Marker |

Reach for the small tools first. `pdftotext` on a clean digital PDF is instant and exact;
MinerU earns its install when the layout is the information.

## Next

The same job with a wider mouth and a pipeline attached →
[Docling](/docs/files/docling/)
