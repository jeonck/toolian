---
weight: 3080
title: "Docling"
description: "One converter for PDFs, Office files, HTML, email, and audio — into a single document model you can export, chunk, and index."
icon: "article"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

[MinerU](/docs/files/mineru/) is a document parser. [Docling](https://docling-project.github.io/docling/)
is a document *pipeline*: the same conversion job, plus a defined document model,
chunking for retrieval, and ready-made integrations with the frameworks you'd feed it
into. It came out of IBM Research Zurich, is hosted by the LF AI & Data Foundation, and
is MIT licensed.

The practical difference shows up in the input list. Alongside PDF, DOCX, PPTX, and XLSX,
it reads HTML, EPUB, LaTeX, plain text, email (EML and MSG), images — and audio and video,
transcribing speech. One command handles a folder that a colleague assembled without
thinking about your tooling.

## Run it

```bash
pip install docling

docling report.pdf
docling https://arxiv.org/pdf/2206.01062
docling ./inbox --output ./out --to md --to json
```

```python
from docling.document_converter import DocumentConverter

result = DocumentConverter().convert("report.pdf")
print(result.document.export_to_markdown())
```

Python 3.10+, runs on macOS, Linux, and Windows, on x86-64 and arm64, and — the property
that decides adoption in a lot of organisations — **entirely locally**, which makes it
usable on documents that must not leave the building.

## DoclingDocument is the actual product

Conversion produces a `DoclingDocument`: a typed structure of pages, blocks, headings,
tables, figures, and their positions, from which the exports are generated —

| Export | Use |
|---|---|
| Markdown | Reading, notes, and prompt context |
| Lossless JSON | Rebuilding the document, keeping coordinates and provenance |
| HTML | Rendering with the layout intact |
| DocTags | A compact tagged form for feeding models |

Because every input format lands in the same structure, downstream code stops caring
whether the source was a PDF or a PowerPoint. That's the piece people underestimate until
they've written the third format-specific branch.

## Built for retrieval, not just conversion

```python
from docling.chunking import HybridChunker

chunker = HybridChunker()
for chunk in chunker.chunk(result.document):
    index(chunk.text, metadata=chunk.meta.export_json_dict())
```

The chunker splits on document structure and token budget together, and each chunk keeps
its heading path and page reference. That metadata is what lets a RAG answer say *which
section on which page* it came from — the difference between a citation and a claim.

Plug-and-play integrations exist for LangChain, LlamaIndex, Haystack, and Crew AI, plus an
**MCP server** and `docling-serve` for an HTTP API, so an agent or a service can call it
without importing the library.

## Docling or MinerU?

They overlap heavily and both run locally. Choose on what surrounds the conversion.

| | [MinerU](/docs/files/mineru/) | Docling |
|---|---|---|
| Focus | Highest-accuracy parsing, especially scans and formulas | Broad input coverage and the pipeline around it |
| Inputs | PDF, images, Office | Those plus HTML, EPUB, email, LaTeX, audio, video |
| Model | Compact VLM, top of the parsing benchmarks | Layout and table models, optional VLM (GraniteDocling) |
| Extras | REST API, MCP, Gradio UI | Document model, chunking, framework integrations, MCP, serve |
| Licence | Apache-2.0-based with added conditions | MIT |

Rough rule: **MinerU** when the documents are hard — dense academic PDFs, poor scans,
heavy mathematics — and parsing quality is the whole job. **Docling** when the documents
are varied and the parsing is one step in a pipeline you're building. Trying both on ten
of your own worst files costs an hour and settles it better than any benchmark.

## The same caution as every parser

Extraction is inference, and confident wrong output looks exactly like right output.
Spot-check tables and figures against the original page before anything downstream treats
them as fact, and keep the source file with the page references from the JSON so a
surprising number can be traced rather than argued about.

## Next

With files under control, on to where you write the code →
[Editors & IDEs](/docs/editor/)
