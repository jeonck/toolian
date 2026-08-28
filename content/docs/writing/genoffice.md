---
weight: 10060
title: "GenOffice"
description: "An open-source office suite with AI built into the document rather than bolted on beside it — and editing that never leaves your machine."
icon: "grid_view"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Most "AI in your documents" is a chat panel on the right-hand side. You ask, it writes,
you copy, you paste, you fix the formatting. The document and the assistant are two
places.

[GenOffice](https://genoffice.ai/) — from Genspark, open-sourced in 2026 under Apache 2.0
— puts the AI inside the document model instead. It opens and saves the real formats,
Word, Excel, and PowerPoint, plus PDF and Markdown, and the assistant edits the document
itself rather than producing text you have to move.

## Install

Installers for **macOS 11+** (Apple Silicon and Intel), **Windows 10+**, and **Linux**
(`.deb`, `.rpm`, or AppImage on x86_64 with glibc 2.34+). From source:

```bash
git clone https://github.com/genspark-ai/genoffice.git
cd genoffice
npm install && npm run dev
```

## The two properties that matter

**Editing is local.** Files are opened, edited, and saved on your machine — nothing is
uploaded to make an edit. That single fact decides whether a tool is usable on a contract,
a payroll sheet, or an internal strategy deck, and it's the reason to look at this rather
than a browser-based suite.

**Round trips are byte-preserving.** The parts of a file you didn't touch come back
identical. Anyone who has opened a colleague's `.docx` in a third-party editor and handed
back a document with re-flowed tables and lost styles knows why this line is on the page.

## Bring your own model, or don't

AI features need the network, and you choose what's behind them: sign in with a Genspark
account, or supply your own API key for Claude, OpenAI, Gemini, DeepSeek and others. The
Genspark Super Agent — research a topic, analyse a sheet, build a deck — draws on Genspark
credits; the editing suite itself is free and ad-free.

Practical consequence worth planning around: **the document stays local, the prompt does
not.** Whatever text you send to the assistant goes to whichever provider you configured.
Check that against your own rules before pointing it at anything confidential, and prefer
your own key when you want the request governed by an agreement you already have.

## Where it fits

| Want | Use |
|---|---|
| Free, open-source office with AI editing, on Linux too | GenOffice |
| The same, with Korean HWP files in the mix | [BatiOffice](/docs/writing/bati-office/) |
| Real-time co-editing with colleagues | Google Workspace or Microsoft 365 |
| Documents as plain text in Git | [Markdown](/docs/writing/markdown/) and an editor |

It's young — open-sourced in 2026 — so treat it as a capable everyday suite rather than a
drop-in replacement for a compliance-critical workflow, and keep the originals until
you've seen a few round trips come back clean.

## Next

The same idea, with the format Korean offices actually run on →
[BatiOffice](/docs/writing/bati-office/)
