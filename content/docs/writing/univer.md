---
weight: 131000
title: "Univer"
description: "An Apache-2.0 SDK for putting real spreadsheets, documents, and slides inside your own product — with a headless Node runtime agents can drive."
icon: "grid_on"
date: "2026-09-28"
lastmod: "2026-09-28"
draft: false
---

Every other tool in this category is one you open. [Univer](https://github.com/dream-num/univer)
is one you build with. It's an Apache-2.0 TypeScript SDK that puts a genuine
spreadsheet, document, or presentation surface *inside* your product — canvas-rendered,
with its own formula engine — instead of sending users out to Excel and back.

The same architecture also runs headless on Node.js. That's the part worth paying
attention to: workbook logic without a browser means an agent can produce and verify a
real `.xlsx` as a step in a pipeline.

## When you'd reach for it

- A SaaS product where users need to edit tabular data properly — not a table component
  pretending to be a spreadsheet.
- An internal tool or BI surface where "export to Excel, edit, re-upload" is the current
  workflow and shouldn't be.
- Server-side workbook processing: recalculate, validate, generate, all in Node.
- An AI application that needs to hand back a real document rather than a Markdown table.

If you just need to *read* a spreadsheet someone sent you, this is the wrong tool and
[GenOffice](/docs/writing/genoffice/) or [BatiOffice](/docs/writing/bati-office/) is the
right one. Univer is for when the spreadsheet is part of what you're shipping.

## Getting something on screen

Two modes. **Preset mode** is the short path:

```bash
pnpm add @univerjs/presets @univerjs/preset-sheets-core
```

```ts
import { createUniver, LocaleType, mergeLocales } from '@univerjs/presets'
import { UniverSheetsCorePreset } from '@univerjs/preset-sheets-core'
import SheetsCoreEnUS from '@univerjs/preset-sheets-core/locales/en-US'

const { univerAPI } = createUniver({
  locale: LocaleType.EN_US,
  locales: { [LocaleType.EN_US]: mergeLocales(SheetsCoreEnUS) },
  presets: [UniverSheetsCorePreset({ container: 'app' })],
})

univerAPI.createWorkbook({})
```

**Plugin mode** is the same thing with every package, style import, locale, and facade
registration spelled out by hand — a dozen `registerPlugin` calls. Verbose, but it's how
you drop the plugins you don't need, which matters because this is a lot of JavaScript
to ship. Start with a preset, move to plugin mode when the bundle bothers you.

The **Facade API** (`univerAPI`) is the layer you actually program against: workbooks,
worksheets, ranges, formulas, commands, and events, with the same shape in the browser
and in Node. Adapters exist for React, Vue, and Web Components.

## The agent angle

The repo now describes itself as "the Office Harness for AI Agents," and the pieces are
concrete rather than marketing:

- **Programmatic editing** — agents read and modify content through structured APIs
  rather than by generating file bytes and hoping.
- **Output verification** — content inspection, rendered screenshots, and layout
  diagnostics, so an agent can check its own work instead of declaring victory.
- **Worktree collaboration** — agents work in isolated drafts; people review and decide
  what merges. The same idea as a git branch, applied to a spreadsheet.

[Univer Workspace](https://github.com/dream-num/univer-workspace) is the reference
implementation — self-hostable, open source, and the fastest way to see the intended
shape before committing. There are also integrations for OpenClaw and a
[Univer CLI](https://github.com/dream-num/univer-cli) that gives an agent a local
command-line workspace for Office content.

## Read the open-source boundary before you plan

This is open core, and the line falls in a place that will matter to you:

| | Open source (Apache-2.0) | Univer Pro (commercial) |
|---|---|---|
| Foundation | Core SDK, plugin system, render and formula engines, Facade API, i18n | Enterprise deployment packages |
| Sheets | Editing, formulas, number formats, filter/sort, validation, conditional formatting, comments, tables | **Collaboration, import/export, print, charts, pivot tables**, sparklines, data connectors |
| Docs | Document model and editor, lists, hyperlinks, comments | Collaboration, import/export, print, columns, code blocks |
| Server | Node headless runtime, RPC/Worker patterns | Collaboration server, SSR, server-side calculation |

**Import/export and charts are Pro.** So is real-time collaboration. A perfectly good
in-app spreadsheet is free; "users can upload their `.xlsx` and download it again" is
not. Price that in at the design stage, not after the demo.

## Honest costs

- **It's a big dependency.** Canvas rendering plus a formula engine is not a small
  bundle. Plugin mode exists precisely because of this.
- **The setup is genuinely verbose.** The plugin-mode example in the README is fifty
  lines before you see a cell. Presets hide it; they don't remove it.
- **You're adopting an architecture**, not a component — plugins, commands, services,
  facades. That pays off when you extend it and hurts if you only wanted a grid.

## Next

Everything so far builds the thing. One category left, on putting it online →
[Vibe Coding Infra](/docs/vibe-infra/)
