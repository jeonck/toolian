---
weight: 130
title: "Self-Hosted"
description: "Services you run on your own hardware instead of subscribing to — a photo library that replaces a cloud plan, and a whole desktop in a browser tab."
icon: "home_storage"
date: "2026-09-07"
lastmod: "2026-09-07"
draft: false
---

At some point the subscriptions stop being small. A photo library that grew past the free
tier, a note app, a file drive, a chat interface — each one is a few dollars a month and
someone else's server holding your data. Self-hosting swaps that recurring bill for a
machine you already own and an evening of setup.

The trade is honest and worth stating up front: **you become the operations team.**
Updates, certificates, disks filling up, and above all backups are now yours. A service
running on one drive in your house is not a backup — it's a single point of failure with
a nice interface.

| Service | Replaces |
|---|---|
| [Immich](/docs/self-hosted/immich/) | Google Photos or iCloud Photos |
| [Puter](/docs/self-hosted/puter/) | A cloud drive plus the small apps around it |

Several tools elsewhere in this guide are self-hosted too — [Stirling
PDF](/docs/files/stirling-pdf/) for documents, [Open WebUI](/docs/ai/open-webui/) in front
of a local model, [Flowise](/docs/automation/flowise/) for AI pipelines, and
[Gitea](/docs/git/gitea/) for code. The same rule applies to all of them: don't expose one
to the internet without authentication in front of it, and know what happens to your data
when the disk dies.
