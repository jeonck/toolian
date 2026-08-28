---
weight: 10070
title: "BatiOffice"
description: "One free desktop app for Word, Excel, PowerPoint, HWP, and PDF — with AI editing that reaches the format Korean offices actually use."
icon: "description"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Working in Korea means HWP. Public-sector forms, university paperwork, and a great deal
of corporate correspondence arrive as `.hwp`, and every cross-platform office tool treats
it as somebody else's problem — so the workflow becomes a licensed copy of one suite for
HWP, another for everything else, and a converter in between that ruins the layout.

[BatiOffice](https://bati.ai/batioffice/) is a desktop app that opens **Word, Excel,
PowerPoint, HWP, and PDF in one place**, on macOS and Windows, with AI editing across all
of them. Opening, editing, and saving local files is free — no account, no subscription,
no cap on how many documents you touch.

## What it does

- **HWP alongside the Microsoft formats**, which is the reason to look at it at all. One
  app for the document a ministry sent you and the deck you're building from it.
- **PowerPoint editing** down to structure, text, images, shapes, and themes, saving back
  to `.pptx`.
- **PDF editing** — text and images, annotations, and page insert, delete, rotate, and
  reorder — rather than a viewer that makes you export somewhere else.
- **AI editing** inside the document, not in a separate chat window.

## Bring your own key

The AI features use **your own API key**. You paste in a key from a provider you already
pay — OpenAI, Anthropic, or another — and BatiOffice stores it encrypted in the operating
system's keychain.

That model has a real advantage and one thing to check. The advantage: no per-seat AI
subscription, and requests are billed and governed under an agreement you already hold.
The thing to check: **your document text still travels to that provider** when you invoke
an AI feature. Local editing is local; a prompt is not. For a sensitive `.hwp` from a
client, decide deliberately which parts you send.

The same company also offers Excel-focused AI — using a model as a spreadsheet function,
and web-based automation for repetitive sheet work — which is worth a look if the
repetitive part of your week lives in Excel.

## Choosing between the office tools here

| Situation | Pick |
|---|---|
| HWP is unavoidable | BatiOffice |
| Open source, and Linux is in the mix | [GenOffice](/docs/writing/genoffice/) |
| Everyone edits the same file at once | Google Workspace or Microsoft 365 |
| The document is really a document you'd rather diff | [Markdown](/docs/writing/markdown/) in Git |

For a Korean workplace the choice is often not either-or: a free suite that handles HWP
locally removes the second licence and the converter, and that is the whole pitch.

## A note on documents you didn't write

Whichever suite you use, a file from outside is untrusted input — macros, embedded
objects, and links included. Open unexpected attachments with the same caution you'd apply
anywhere else, and don't let a document convince an AI assistant to act on instructions
buried in its text.

## Next

Everything so far builds the thing. One category left, on putting it online →
[Vibe Coding Infra](/docs/vibe-infra/)
