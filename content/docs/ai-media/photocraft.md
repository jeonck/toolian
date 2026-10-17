---
weight: 150950
title: "PhotoCraft"
description: "A clean-room reimplementation of Photoshop in pure Rust — real PSD files, adjustment layers, 67 tools, and a 500+ command registry that an MCP server or a CLI can drive headlessly."
icon: "layers"
date: "2026-10-17"
lastmod: "2026-10-17"
draft: false
---

[Drift](/docs/ai-media/drift/) is the video-editing version of this idea: an ordinary
desktop app with an MCP server bolted on so an agent can work the timeline beside you.
[PhotoCraft](https://github.com/storytold/photocraft) does the same thing for raster
image editing, except it isn't wrapping an existing editor — **it's Photoshop itself,
rewritten from the public spec in pure Rust**, with every menu item, tool, and dialog
backed by one of 500+ commands that the UI, the CLI, a JSON control channel, and an MCP
server all call the same way.

Dual-licensed MIT/Apache-2.0, currently **early alpha**.

## What it actually opens

Not a PNG viewer with filters. Real layered documents: PSD and PSB, layered TIFF with
Photoshop's own layer data, Paint.NET `.pdn` (editable layers), OpenRaster, Krita `.kra`,
SVG as shape layers, plus the usual flat formats down to FITS astronomy images and OpenEXR
at full float precision. Affinity files (`.af`, `.afdesign`, `.afphoto`) open read-only.

The PSD claim is backed by a corpus, not a demo screenshot: **re-saving renders the same
pixels for 307 of 309 files in the psd-tools test set.** Worth the precise wording —
re-saving rewrites image resources and layer records, so the output isn't byte-identical
to the source; only the standalone `photocraft-psd` crate, which parses and writes
without the full document model, reproduces files byte for byte. Anything the document
model doesn't understand yet (raw blocks, descriptors) is carried through rather than
silently dropped.

## The editing surface

16 adjustment layers (Curves, Levels with a live histogram, Vibrance, Selective Color,
Gradient Map, Color Lookup via `.cube`/`.3dl`/`.look`...) that stack non-destructively or
apply straight to pixels. Layer styles — Drop Shadow, Glow, Bevel, Stroke, Pattern
Overlay — live on any layer including type, and copy-paste between layers. 67 tools
including Object Selection and Select Subject for automatic masking, entirely **local —
no cloud, no account**. Full ICC color management (RGB/Grayscale/CMYK/Lab at 8/16/32-bit)
with soft proofing and gamut warning on the GPU. 27 Photoshop blend modes plus six from
Paint.NET. The Pen, Freeform Pen, and Curvature Pen produce resolution-independent vector
shape layers that match Photoshop pixel-for-pixel in 116 of 116 corpus test cases.

Every filter dialog previews live, through the active selection, before you commit — and
a radius-180 Gaussian blur on a 3.6 MP image runs in under a second, using running-sum box
passes across every core.

## Driving it from outside the UI

This is the section that puts it on this page rather than a design-tools roundup
elsewhere in the site:

```bash
# Headless: open, edit, save — no UI process at all
photocraft-cli run wave.psd \
  --cmd filter.sharpen.smartSharpen     --params '{"amount":80}' \
  --cmd layer.newAdjustmentLayer.curves --params '{"points":[[0,0],[64,48],[192,212],[255,255]]}' \
  --out wave-final.png

# One action list over a whole folder
photocraft-cli batch --actions grade.json --in ./raw --out ./graded

# Hand it to an agent
photocraft-cli mcp
```

Because the UI is a thin layer over the same command registry the CLI calls, nothing an
agent does through MCP is a special code path that might drift from what a human clicking
the menu gets. The desktop app separately exposes an authenticated, **loopback-only**
control channel for inspecting UI state, driving tools with synthetic pointer events, and
taking offscreen screenshots — every screenshot in the project's own README was rendered
that way, which is a fairly convincing demo of the headless path actually working.

## Under the hood, briefly

Pure-data document model plus a command engine, with a thin egui UI on top — layering
enforced at build time across 24 crates, so the UI can't reach past the engine's own
API. Two compositors, CPU and GPU (wgpu: Metal/Vulkan/DX12/WebGPU), tested against each
other as mutual oracles. Copy-on-write 256² tiles keep undo cheap on huge canvases. The
whole engine and UI also compile to WebAssembly and run in a browser. Clean-room: built
from public specs and observed behaviour, no proprietary code, shaders, or assets. Over
1,700 tests.

## Get started

```bash
git clone https://github.com/storytold/photocraft
cd photocraft
cargo run --release -p photocraft -- image.psd   # desktop app
cargo test --workspace                           # the test suite
```

Prebuilt installers for macOS (signed, notarized), Windows (code-signed .msi, x64/arm64/
x86), and Linux (AppImage, Flatpak, .deb, .rpm, tarball, plus a RISC-V tarball) are on the
[releases page](https://github.com/storytold/photocraft/releases/latest); FreeBSD and a
static WebAssembly build round it out. Easiest path for most people is the download link
on [getartcraft.com](https://getartcraft.com/apps/photocraft) rather than picking a build
by hand.

One Linux-specific snag worth knowing before it costs you an hour: **on Wayland, dropping
a file onto the window doesn't work yet** (winit has no Wayland drag-and-drop). Use File
› Open, paste with Ctrl+V, or run under XWayland — the app shows the exact command for
your install type in a notice until you dismiss it.

## Where it actually stands

The project is upfront about this rather than letting the feature table speak for it:
**it is not yet a Photoshop replacement for daily professional work.** Every Photoshop
menu item is wired to a command, but that measures wiring, not behaviour — the stated
gaps are generative/AI features, 15 missing tools, depth in typography and pro workflows,
and plugin compatibility. Read the [parity checklist](https://github.com/storytold/photocraft/blob/main/docs/parity-checklist.md)
before you bet real client work on it.

## Part of a family

PhotoCraft is one of ArtCraft's **Crafting Apps** — free, open-source, clean-room Rust
rewrites of a whole creative suite, each standing on its own: VectorCraft (illustration),
FilmCraft (video, color, sound), LightCraft (photo library, raw development), PdfCraft,
EffectCraft (motion graphics), and DesignCraft (page layout). Same conventions
throughout — native cross-platform, WebAssembly build, agent-drivable.

## Next

Generating the footage rather than cutting it → [Google Flow](/docs/ai-media/flow/)
