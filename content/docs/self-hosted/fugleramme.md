---
weight: 160400
title: "Fugleramme"
description: "A Raspberry Pi picture frame that listens to your garden, identifies the birds locally, and draws them as hand-cut 1800s natural-history plates."
icon: "filter_frames"
date: "2026-09-26"
lastmod: "2026-09-26"
draft: false
---

Everything else in this category replaces a subscription. [Fugleramme](https://github.com/arnegiacomo/fugleramme)
replaces nothing — it's here because it's the most enjoyable thing you can do with a
Raspberry Pi and an afternoon, and because it's a genuinely good demonstration of local
AI doing something worth doing.

A mic listens to your garden. [BirdNET-Go](https://github.com/tphakala/birdnet-go)
identifies the species from their calls, entirely on the device, with nothing leaving the
house. Fugleramme polls its API, matches each bird to a public-domain illustration cut
from a real 19th-century plate, packs them onto a textured paper page, and redraws when
the birds change. The result hangs on a wall and shows the birds that are *actually
outside right now*.

The author's own frame is live at
[fugleramme.arnegiacomo.dev](https://fugleramme.arnegiacomo.dev), showing what's in a
garden in Bergen at this moment.

## You don't need the e-ink panel

The nicest version is an Inky Impression 13.3" in an A4 frame on a Raspberry Pi 5. But
the panel is optional — without one it runs web-only and the page takes the shape of
whatever displays it: a TV, an HDMI monitor, a tablet on a shelf, or your desktop
wallpaper. That makes it a zero-hardware thing to try first.

```bash
docker run -d -p 8080:8080 -v fugleramme:/data \
  -e FUGLERAMME_DETECTOR_URL=http://birdnet.local:8080 \
  ghcr.io/arnegiacomo/fugleramme
```

Kiosk on `:8080`, admin on `:8080/admin`, state in `/data`. Already running BirdNET-Go?
Point `FUGLERAMME_DETECTOR_URL` at it, anywhere on your network.

Nothing running yet — a Linux box with a USB mic brings up both:

```bash
curl -fsSL https://raw.githubusercontent.com/arnegiacomo/fugleramme/main/examples/docker-compose.yml -o docker-compose.yml
docker compose up -d
```

On a Pi, the installer does the whole thing — BirdNET-Go, dependencies, and a systemd
service:

```bash
curl -fsSL https://raw.githubusercontent.com/arnegiacomo/fugleramme/main/install.sh | bash
```

And to poke at it on a laptop with no hardware at all, there's a fake detector:

```bash
uv sync
uv run fugleramme-fake-detector    # stand-in BirdNET-Go on :8090
uv run fugleramme-dev              # frame on :8080, hot reload
```

## Why the art matters

Over 800 cut-outs covering 400+ species, every one taken from a real plate and
hand-curated. **None of it is AI-generated** — some has been retouched, and the manifest
links back to the plate each file was cut from.

The layout is doing more work than it looks: larger birds toward the centre, each sized
by actual body mass (from the AVONET dataset), and an empty window drawn as a bare perch
when nothing has been heard. That's the difference between a dashboard and something you
want on a wall.

Coverage follows the source plates — Scandinavian, British, and central European, so the
Nordics, the British Isles, and Germany are well served and elsewhere is thinner. Wider
European and North American coverage is in progress, and
[adding artwork](https://github.com/arnegiacomo/fugleramme/blob/main/docs/adding-artwork.md)
is the contribution the project asks for most.

## Before you build one

- **Early development.** The README says so plainly: expect rough edges.
- **Licensing is layered.** Code is MIT, but BirdNET-Go's detection model is
  **CC BY-NC-SA 4.0 — non-commercial only**, and the `classic` artwork is CC BY-SA 4.0.
  Fine for a frame in your kitchen; read the terms before anything commercial.
- **Geography decides the payoff.** Outside the well-covered regions, more of your birds
  will come up without an illustration.
- **It needs a decent mic.** Detection quality is an audio problem before it's a model
  problem.

## Why it's in this knowledge base

Because it's the clearest small example of the pattern the rest of this site keeps
circling: a model small enough to run on a $80 computer, doing one narrow job well, with
no account, no API key, and no data leaving the building. [Ollama](/docs/ai/ollama/)
makes that argument abstractly. A frame that knows a blue tit just landed outside makes
it concrete.

## Next

An agent with its own browser, terminal, and files, on your hardware →
[OpenMuse](/docs/self-hosted/openmuse/)
