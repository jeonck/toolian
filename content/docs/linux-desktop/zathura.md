---
weight: 9530
title: "Zathura"
description: "A PDF reader driven entirely from the keyboard, with Vim bindings and a colour inversion that saves your eyes at night."
icon: "picture_as_pdf"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

A PDF viewer with a toolbar, a sidebar, and a ribbon is optimised for someone filling in
a form. If you mostly *read* — papers, specs, manuals — the interface is in the way, and
every navigation is a trip to the mouse.

[Zathura](https://pwmt.org/projects/zathura/) shows the document and nothing else. It's
driven from the keyboard with Vim bindings, starts instantly, and one keystroke inverts
the colours for reading at night.

## Install

```bash
sudo apt install zathura zathura-pdf-mupdf
sudo dnf install zathura zathura-pdf-mupdf
sudo pacman -S zathura zathura-pdf-mupdf
```

The viewer itself renders nothing — support comes from plugins. Pick **one** PDF backend:
`zathura-pdf-mupdf` (better font rendering, lighter on large files, the usual
recommendation) or `zathura-pdf-poppler`. Installing both is the classic mistake — which
one gets used is then unpredictable. There are separate plugins for DjVu, PostScript, and
EPUB.

## Read something

```bash
zathura paper.pdf
```

| Key | Does |
|---|---|
| `j` / `k` | Scroll down / up |
| `d` / `u` | Half page down / up |
| `J` / `K` | Next / previous page |
| `gg` / `G` | First / last page |
| `5G` | Go to page 5 |
| `/text` then `n` / `N` | Search, next, previous match |
| `Tab` | Table of contents; navigate and `Enter` to jump |
| `a` / `s` | Fit page / fit width |
| `+` / `-` | Zoom |
| `r` | Rotate |
| `Ctrl+r` | **Invert colours** |
| `f` | Show link hints — press the number to follow a link |
| `o` | Open another file without leaving |

If you use Vim, you already know most of this. `Ctrl+r` is the one to remember: white
pages at midnight become a dark document, and the setting can be made the default.

## A config worth having

`~/.config/zathura/zathurarc`:

```
set recolor true                       # start in dark mode
set recolor-lightcolor "#1e1e2e"
set recolor-darkcolor  "#cdd6f4"
set recolor-keephue true               # keep figures recognisable
set recolor-reverse-video true         # don't invert images and diagrams

set selection-clipboard clipboard      # yanked text goes to the normal clipboard
set adjust-open width
set statusbar-home-tilde true
set scroll-step 80

map <C-i> zoom in
map <C-o> zoom out
map r reload
```

`recolor-reverse-video` is the line that makes dark mode usable for technical documents —
without it, every diagram and photograph is inverted too.

## Where it fits

| Want | Use |
|---|---|
| Read a document, quickly, from the keyboard | Zathura |
| Annotate, fill forms, sign, edit | Okular, or a full PDF editor |
| Follow references while reading papers | Sioyek — same spirit, built for research |
| Merge, split, extract, or OCR | The command line — `qpdf`, `pdftk`, `ocrmypdf` |

It pairs naturally with a LaTeX or Typst workflow: Zathura reloads on file change, so the
rendered document updates as you write, and SyncTeX support jumps between source line and
page.

## Next

With the desktop sorted, on to writing things down →
[Docs & Notes](/docs/writing/)
