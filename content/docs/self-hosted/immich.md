---
weight: 13010
title: "Immich"
description: "Your own Google Photos — phone backup, albums, face recognition, and search by what's in the picture, on your hardware."
icon: "photo_library"
date: "2026-09-07"
lastmod: "2026-09-07"
draft: false
---

Photo storage is the subscription that never goes down. A few hundred gigabytes of
holidays and screenshots quietly becomes a permanent monthly line item, and the exit is
worse than the price — a decade of memories held in a service you can't easily leave.

[Immich](https://github.com/immich-app/immich) is the same product, self-hosted: automatic
phone backup, albums, sharing, a map, memories, facial recognition, and search that
understands the *contents* of a photo, not just its filename. AGPL-3.0, and it does not
feel like a compromise.

## Install

```bash
mkdir immich && cd immich
wget -O docker-compose.yml https://github.com/immich-app/immich/releases/latest/download/docker-compose.yml
wget -O .env https://github.com/immich-app/immich/releases/latest/download/example.env
# edit .env: UPLOAD_LOCATION and DB_DATA_LOCATION
docker compose up -d
```

Then the web UI on port 2283, and the mobile app (iOS and Android) pointed at your
server's address. There's a [live demo](https://demo.immich.app) if you'd rather look
before installing.

## What it does

| | Mobile | Web |
|---|---|---|
| Background and automatic backup | Yes | — |
| Albums, shared albums, partner sharing | Yes | Yes |
| Search by metadata, objects, faces, and CLIP | Yes | Yes |
| Facial recognition and clustering | Yes | Yes |
| RAW formats, Live/Motion Photos, 360° | Yes | Mostly |
| EXIF and map view, global map | Yes | Yes |
| Multi-user, OAuth, API keys | Partly | Yes |
| Memories — "x years ago" | Yes | Yes |

The CLIP search is the feature that wins people over: type "red umbrella on a beach" and
it finds the photo, with the model running on your own machine rather than someone's
cloud. Face clustering works the same way, locally.

## Read this before you trust it with anything

- **The project's own warning comes first for a reason: follow a 3-2-1 backup plan.**
  Three copies, two kinds of media, one off-site. Immich running on one drive is a photo
  *library*, not a backup — and "the container is up" is not the same as "the data is
  safe." Back up the upload directory *and* the Postgres database; restoring one without
  the other gets you files with no albums or faces.
- **Machine learning wants memory.** The search and face models run on your server. A
  small NAS or a Raspberry Pi will do it, slowly; expect the first full library scan to
  take a long time. The ML container can be pointed at a stronger machine.
- **Don't put it on the open internet casually.** Reach it over
  [Tailscale](/docs/network/tailscale/) or a reverse proxy with real authentication.
- **Keep an eye on release notes when updating.** Immich moves fast and occasionally
  requires a migration step; read before you `docker compose pull`.

## Next

A whole computing environment in a browser tab → [Puter](/docs/self-hosted/puter/)
