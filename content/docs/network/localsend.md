---
weight: 7055
title: "LocalSend"
description: "AirDrop that doesn't care what the other device is — files between phone and laptop over your own network, no cloud in the middle."
icon: "share"
date: "2026-09-07"
lastmod: "2026-09-07"
draft: false
---

Moving a file from an iPhone to a Windows laptop usually means emailing it to yourself,
or uploading it to a cloud drive so it can come straight back down again.
[LocalSend](https://github.com/localsend/localsend) removes the round trip: pick the
device, send the file, done — **over your local network, with no account and no internet
connection**.

Windows, macOS, Linux, Android, iOS, and Fire OS, all talking to each other. Apache 2.0.

## Install

Prefer a package manager or app store — the app has no auto-update, so a store keeps it
current.

```bash
winget install LocalSend.LocalSend      # Windows
brew install --cask localsend           # macOS
flatpak install flathub org.localsend.localsend_app
```

Android is on Play and F-Droid, iOS on the App Store; `.deb`, `.rpm`, AppImage, Snap, AUR,
and Nixpkgs builds all exist.

## How it works

Devices discover each other on the LAN and transfer over a REST API on port `53317`,
encrypted with HTTPS using a TLS certificate each device generates for itself. No server
sits in the middle, so nothing is uploaded, nothing is retained, and speed is whatever
your network can do rather than whatever your upload link can do. The
[protocol](https://github.com/localsend/protocol) is documented and open.

## From the terminal

```bash
localsend-cli send report.pdf photo.jpg ./project-backup
localsend-cli send --to "Cute Tomato" report.pdf
localsend-cli send --to 192.168.27.26 report.pdf
```

Without `--to` it shows the discovered devices and you pick one. Directories are collected
recursively and their structure is preserved on the receiver — though empty directories
aren't sent, because the protocol moves files, not folders.

## When it can't see the other device

This is the one problem you'll actually have, and it's almost always the network rather
than the app:

| Symptom | Usual cause |
|---|---|
| Nothing appears, any platform | AP isolation on the router — it forbids device-to-device traffic |
| Nothing appears, receiving on Windows | The network is set to "public"; switch it to private |
| Nothing appears, sending from macOS or iOS | Toggle the Local Network permission in Privacy settings |
| Nothing appears, VPN running | Allow LAN traffic, or drop the VPN for a moment |
| Still nothing | Send manually to the receiver's IP, then favourite the device so it's probed directly |
| Transfers crawl | Use 5 GHz rather than 2.4, and consider turning encryption off on both ends |

That last one is a real trade: the speed comes from skipping TLS, which is fine on your
own network and not fine on a café's.

## Where it fits

[Tailscale](/docs/network/tailscale/) puts your machines on one private network wherever
they are; LocalSend moves a file between two devices already sitting on the same one. If
the phone and the laptop are in the same room, this is the shorter path — and it's the
tool to reach for when the file is too big to email and too private to upload.

## Next

For pulling data out of sites instead of pushing requests at them →
[AutoCLI](/docs/network/autocli/)
