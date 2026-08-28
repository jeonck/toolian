---
weight: 10050
title: "Excalidraw"
description: "A hand-drawn-feel whiteboard, well suited to design discussion and architecture sketches."
icon: "draw"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Mermaid is strong on formalised diagrams, but it's the wrong tool for an architecture
sketch that needs free placement, or for drawing live in a meeting. Excalidraw fills
that gap, and its hand-drawn style sends a useful signal: **this isn't settled yet.**

## Getting started

Open [excalidraw.com](https://excalidraw.com) and draw. No account, no install.

To use it locally:

- **VS Code**: install the `Excalidraw` extension and create a `.excalidraw` file
- **Obsidian**: the Excalidraw plugin
- **Self-hosted**: `docker run -p 5000:80 excalidraw/excalidraw`

## Essential shortcuts

| Key | Tool |
|---|---|
| `1` `V` | Select |
| `2` `R` | Rectangle |
| `3` `D` | Diamond |
| `4` `O` | Ellipse |
| `5` `A` | Arrow |
| `6` `L` | Line |
| `8` `T` | Text |
| `9` | Image |
| `Space` drag | Pan the canvas |
| `⌘D` | Duplicate |
| `⌥` drag | Duplicate while moving |

## Keeping it tidy

- **Bind arrows to shapes.** Start an arrow on a shape's edge and it attaches, so moving
  the shape later drags the arrow with it.
- **Put text inside shapes.** Double-click a shape and the text lives in it and moves
  with it.
- **Use two or three colours.** Grey for the default, blue for emphasis, red for
  problems is plenty.
- **Let the tool align.** Select several elements, right-click → Align.
- **Group things** (`⌘G`) so you can move a cluster at once later.

## Libraries

Pull in sets of shapes from
[libraries.excalidraw.com](https://libraries.excalidraw.com) — AWS and GCP icons,
software architecture elements, wireframe parts, and more.

## Saving and sharing

| Method | Character |
|---|---|
| `.excalidraw` file | The editable original. Commit it |
| PNG export | With "Embed scene" on, the PNG carries the source and stays editable |
| SVG export | Scales cleanly. Good for embedding in documents |
| Live collaboration link | Real-time editing, end-to-end encrypted |

**The "Embed scene" option on PNG export** is especially useful: put only the PNG in a
README, and dragging that PNG back into Excalidraw later reopens it for editing.

## Keeping it with the code

```
docs/
  architecture.md
  diagrams/
    system-overview.excalidraw     # editable source
    system-overview.svg            # embedded in the doc
```

```markdown
![System overview](./diagrams/system-overview.svg)
```

Commit the source alongside and you can still change it six months later. With only the
SVG, you're effectively redrawing.

## Splitting the work with Mermaid

| Situation | Tool |
|---|---|
| Flowcharts, sequences, ER | Mermaid (fixed syntax, faster) |
| Architecture overviews where layout carries meaning | Excalidraw |
| The change needs to be readable in a PR | Mermaid |
| Sketching live in a meeting | Excalidraw |

Excalidraw can also convert pasted Mermaid code into shapes — build the skeleton in
Mermaid, then loosen it by hand.

## Next

Everything so far builds the thing. One category left, on putting it online →
[Vibe Coding Infra](/docs/vibe-infra/)
