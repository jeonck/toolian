---
weight: 3090
title: "Stirling PDF"
description: "Fifty-plus PDF operations — merge, split, OCR, redact, sign, convert — running on your own machine instead of a stranger's upload form."
icon: "picture_as_pdf"
date: "2026-09-07"
lastmod: "2026-09-07"
draft: false
---

[MinerU](/docs/files/mineru/) and [Docling](/docs/files/docling/) turn a PDF into text
you can work with. [Stirling PDF](https://github.com/Stirling-Tools/Stirling-PDF) does the
other half of the job: operating on the PDF itself. Merge, split, rotate, compress,
convert, OCR, sign, watermark, and **redact** — fifty-plus tools in one place.

The real argument for it isn't the feature count. It's that the alternative is uploading
a contract to a free PDF site whose business model you have never once thought about.

## Run it

```bash
docker run -p 8080:8080 docker.stirlingpdf.com/stirlingtools/stirling-pdf
```

Open `http://localhost:8080` and it's the whole toolbox in a browser tab. There's also a
desktop client and a Kubernetes path if you'd rather not keep a container around, and the
UI ships in 40+ languages.

## What's in it

| Group | Tools |
|---|---|
| Page work | Merge, split, reorder, rotate, crop, delete pages, N-up |
| Size | Compress, downsample images |
| Convert | To and from images, Office formats, HTML, Markdown |
| Text | OCR, extract text, add page numbers, watermarks |
| Documents | Sign, fill forms, flatten, compare |
| Security | Password, permissions, redact, sanitise, remove metadata |

Two of these deserve their own note. **OCR** turns a scanned page into a searchable PDF,
which is what makes an archive of scans usable at all. And **redaction** here means
actually removing the content, unlike drawing a black rectangle in a viewer — the classic
way to publish a document whose secrets can be recovered by selecting the text underneath.

## Beyond clicking

Nearly every tool has a REST endpoint, so a job you do monthly becomes a script:

```bash
curl -X POST http://localhost:8080/api/v1/general/merge-pdfs \
  -F "fileInput=@a.pdf" -F "fileInput=@b.pdf" -o merged.pdf
```

There are also no-code pipelines in the UI for chaining operations across a folder of
files — OCR everything, compress, then split by bookmark, on a schedule.

## Worth knowing

- **It's open-core, not plain open source.** The bulk is MIT, and specific directories in
  the repository (`proprietary/`, `saas/`, `engine/`, parts of the desktop and portal
  code) carry their own licences. Self-hosting the free build for your own work is fine;
  read the terms before you embed it in a product or resell it. SSO, auditing, and the
  enterprise features are paid.
- **Nothing leaves your machine, which is the point** — but that also means the security
  of the deployment is yours. If you expose it beyond localhost, put authentication in
  front of it. A public Stirling instance is an open document-processing endpoint.
- **Some conversions are approximations.** PDF-to-Office in particular gets the content
  out but rarely the layout intact. Check the result before sending it to anyone.

## Next

Every tool so far assumes a document should become text. One disagrees →
[PixelRAG](/docs/files/pixelrag/)
