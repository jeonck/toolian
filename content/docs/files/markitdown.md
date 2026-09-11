---
weight: 3085
title: "MarkItDown"
description: "Microsoft's converter that turns PDF, Office, audio, images and a dozen other formats into Markdown an LLM can read — breadth first, fidelity second."
icon: "text_snippet"
date: "2026-09-11"
lastmod: "2026-09-11"
draft: false
---

[MinerU](/docs/files/mineru/) and [Docling](/docs/files/docling/) go deep on documents.
[MarkItDown](https://github.com/microsoft/markitdown) goes wide: one `pip install`, one
command, and roughly any file on your disk comes out as Markdown.

The trade-off is stated plainly in its own README, which is refreshing — the output "may
not be the best option for high-fidelity document conversions for human consumption." It
is built to feed text-analysis pipelines, not to reproduce your document. Mainstream
models speak Markdown natively and it's token-efficient, so that's the target format.

## What it eats

PDF · Word · PowerPoint · Excel · HTML · CSV, JSON, XML · EPub · images (EXIF plus OCR) ·
audio (EXIF plus speech transcription) · YouTube URLs · ZIP archives, iterating over the
contents.

That ZIP line is the one people underrate: point it at an archive of mixed attachments and
it walks the whole thing.

## Install and run

Python 3.10+, in a virtualenv:

```bash
pip install 'markitdown[all]'
markitdown report.pdf > report.md
markitdown report.pdf -o report.md
cat report.pdf | markitdown          # it reads stdin too
```

`[all]` pulls every optional dependency. If you know what you're handling, install less:

| Extra | For |
|---|---|
| `[pdf]` `[docx]` `[pptx]` `[xlsx]` `[xls]` | The obvious ones |
| `[outlook]` | Outlook messages |
| `[audio-transcription]` | Speech in wav and mp3 |
| `[youtube-transcription]` | Fetching YouTube transcripts |
| `[az-doc-intel]` `[az-content-understanding]` | The two Azure backends below |

```bash
pip install 'markitdown[pdf, docx, pptx]'
```

## When the built-in converters aren't enough

Three escape hatches, in increasing order of cost:

**Plugins**, disabled by default — `markitdown --list-plugins`, then
`markitdown --use-plugins file.pdf`. Search GitHub for the `#markitdown-plugin` hashtag.
The first-party `markitdown-ocr` adds OCR to the PDF, DOCX, PPTX and XLSX converters by
sending embedded images through an LLM vision model, reusing the same
`llm_client`/`llm_model` pair MarkItDown already takes for image descriptions. No new
binary dependencies. Note the failure mode: **with no `llm_client`, the plugin still loads
and OCR is silently skipped** — you get the plain converter's output and no error.

**Azure Document Intelligence** for cloud layout extraction:

```bash
export MARKITDOWN_DOCINTEL_ENDPOINT="<endpoint>"
markitdown scan.pdf -o scan.md -d
```

**Azure Content Understanding** is the only path that handles **video**, and the only one
that extracts structured fields — invoice amounts, contract dates — as YAML front matter
on the output:

```bash
export MARKITDOWN_CU_ENDPOINT="<endpoint>"
markitdown report.pdf --use-cu
```

Both Azure routes bill per `convert()` call. In the Python API, `cu_file_types` restricts
which formats route to the cloud so a batch of CSVs doesn't quietly become an invoice:

```python
from markitdown import MarkItDown
from markitdown.converters import ContentUnderstandingFileType

md = MarkItDown(cu_endpoint="<endpoint>", cu_file_types=[ContentUnderstandingFileType.PDF])
```

## Read this before you put it behind a web form

MarkItDown does I/O with the privileges of the calling process. Like `open()` or
`requests.get()`, it reaches whatever that process can reach — and `convert()` is
deliberately permissive, accepting local paths, remote URIs, and byte streams alike.

Hand it a user-supplied string and you have handed a user your filesystem and your
internal network, metadata endpoints included. The project's own guidance is to **call the
narrowest function that does the job**:

| Use | Call |
|---|---|
| Local files only | `convert_local()` |
| You fetch the URL yourself | `convert_response()` |
| Maximum control | `convert_stream()` |

In anything hosted, validate paths, restrict URI schemes, and block private, loopback,
link-local and metadata addresses before the call — not inside it.

## Scope, deliberately narrow

The repository accepts converter fidelity work, bug fixes, the CLI, and the
`markitdown-mcp` package — and explicitly refuses web servers, REST APIs, hosted
services, browser frontends, and desktop apps. Those are expected to live as separate
projects depending on the PyPI package. If you were hoping to upstream a GUI, don't; build
it alongside.

## Next

Reading a PDF is one job; operating on one is another →
[Stirling PDF](/docs/files/stirling-pdf/)
