---
weight: 12080
title: "Drift"
description: "A free desktop video editor with an MCP server, so an agent can work the timeline beside you."
icon: "movie_edit"
date: "2026-09-07"
lastmod: "2026-09-07"
draft: false
---

Video work has moved in three steps: editing every cut by hand, then generating clips
from a prompt, and now — the interesting one — an agent operating the editing program
itself. [Drift](https://github.com/CutWire-Studios/Drift) is the third. It's an ordinary
GPL-3.0 desktop editor built on Qt 6 and FFmpeg, with **a built-in MCP server** that lets
Claude Code, Cursor, or any compatible agent edit the project you have open.

No account, no watermark, no subscription. Linux, Windows, macOS, and Android.

## Install

```bash
flatpak install flathub org.cutwire.Drift
```

AppImage, a Windows installer and portable zip, an Apple Silicon `.dmg`, and Android APKs
are all on the [releases page](https://github.com/CutWire-Studios/Drift/releases/latest).
The macOS build is signed ad-hoc rather than notarised, so the first launch needs
right-click → Open:

```bash
xattr -dr com.apple.quarantine /Applications/Drift.app
```

Fonts, stickers, extra effects, and speech models aren't bundled — pull only the packs you
need from the Addon Manager in the header.

## As an editor

A real multi-track timeline (trim, split, snap, ripple, undo), GPU effects and
transitions, titles and stickers on canvas, auto captions you can correct, subject cutouts
and green screen, speed ramps and beat-synced cuts, an audio chain with EQ, compression
and noise cleanup, multicam, and project bundles that move media along with the edit.
Export is MP4 with presets, GIF, or a ranged In/Out export — and the preview is the
compositor, so what you signed off on is what you get.

That's a complete editor before any of the AI part. Which matters: an agent hook on a
weak editor just automates a weak editor.

## Agent access

Turn it on in **Settings → Agent access**. It's off at every launch, and it listens only
on your own machine; the bearer token rotates each session.

The agent doesn't get a chat box bolted to a web page — it gets the project. It can import
media, place and trim clips, change effects, capture a still to check its own work, and
export. Every edit lands in the undo stack, which is the safety net that makes this
tolerable at all.

### How the agent sees it

| Call | What it does |
|---|---|
| `catalog` | Lists the toolboxes and when to use each op |
| `toolbox({name})` | Full JSON schemas for that toolbox |
| `apply({ops:[…]})` | Runs mutations in order — **one undo step for the whole batch** |
| `inspect({clips:true})` | Project state: clip UUIDs, effects, transitions, subtitles, jobs |
| `capture()` | A JPEG still of the composition, to verify the edit |

Attaching to a running editor without putting a token in a config file:

```json
{
  "mcpServers": {
    "drift": {
      "command": "/path/to/drift",
      "args": ["--mcp-stdio"]
    }
  }
}
```

If the editor is closed or Agent access is off, calls return a JSON-RPC error and the
bridge stays up — flipping the setting is enough, no client restart.

### Headless, on a server

```bash
drift --headless                          # MCP on stdin/stdout
drift --headless --mcp-port 4731          # MCP over HTTP
```

No window, no open project, no session token needed. One catch worth knowing before you
debug it at midnight: **rendering needs an OpenGL 3.3 context, which is not the same as
needing a window.** On a Linux box with no `/dev/dri`:

```bash
QT_QPA_PLATFORM=xcb xvfb-run -a drift --headless
```

Without a usable context it still serves MCP and still edits projects — only render,
capture, and export fail.

## Two ways to let an agent make video

| | Drift | [HyperFrames](/docs/ai/hyperframes/) / [Remotion](/docs/ai/remotion/) |
|---|---|---|
| The video is | A project file in an editor | Source code in your repository |
| The agent | Operates the timeline | Writes the composition |
| Review | Watch it, scrub it, undo it | Read the diff |
| Fits | Footage you shot, edited once | Templated video regenerated on every release |

They're not competitors. One is for the edit you'd otherwise do by hand; the other is for
video that should rebuild itself.

## Worth knowing

- **It's young.** v0.5.x, and moving fast. Keep projects backed up as bundles and don't
  put an unmissable deadline on a release you installed yesterday.
- **Agent access is off by default, and that's the right default.** Turn it on for the
  session you want it, not permanently.
- **An agent is fast at the tedious half** — importing, syncing, applying the same
  treatment to forty clips, generating captions. Taste in the cut is still yours.

## Next

Services worth running on your own hardware → [Self-Hosted](/docs/self-hosted/)
