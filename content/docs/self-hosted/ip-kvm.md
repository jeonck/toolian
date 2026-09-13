---
weight: 13030
title: "IP KVM"
description: "A box that gives you the screen, keyboard, power button and boot media of a machine you can't reach — and, because of exactly that, the most dangerous thing on your network."
icon: "settings_remote"
date: "2026-09-13"
lastmod: "2026-09-13"
draft: false
---

Self-hosting goes fine until the evening it doesn't. A kernel update leaves the box
sitting at an initramfs prompt, a BIOS setting needs changing, the machine hangs before
the network comes up. SSH is dead precisely when you need it, and the fix is a walk to
wherever the hardware lives.

An **IP KVM** removes that walk. It sits between you and the machine as physical
hardware, so it doesn't care whether the operating system is running, booting, or
gone.

| What it does | How |
|---|---|
| Shows the screen | Captures the HDMI/VGA output — BIOS, boot loader, safe mode, kernel panic, all of it |
| Types and clicks | USB HID emulation: the host sees a real keyboard and mouse |
| Presses the buttons | Wired to the motherboard's power and reset pins, so a hard power cycle is remote |
| Mounts a disk | Serves an ISO as a USB mass-storage device — remote OS reinstall from bare metal |

That last row is the one that changes what's possible. Screen and keyboard let you
*diagnose* a dead machine; virtual media lets you *rebuild* it without being in the
room.

Server-grade boards have had this forever as IPMI, iDRAC or iLO. What changed is that the
same capability now costs less than a mid-range SSD and clips onto a desktop, a NUC or a
Raspberry Pi.

## The 2026 field

| Device | Roughly | The short version |
|---|---|---|
| **PiKVM V4 Mini** | $270–280 | The most capable open-source unit — GPLv3, IPMI and Redfish emulation, deepest feature set, cleanest security record |
| **JetKVM** | ~$103 | Best hardware feel and momentum in the category; the usual pick as a daily driver |
| **Luckfox PicoKVM** | $28 / $56 | RV1106, 1080p60 H.264 capture at 80–200 ms, Wake-on-LAN, GPIO power control; Full adds a 1.54" touchscreen |
| **Sipeed NanoKVM Full** | ~$70 | Real capability for very little money — read the next section before buying |

Features differ most on **virtual media, Wi-Fi, and ATX power control**, and those are
exactly the ones that decide whether the device solves your actual problem. Check the
current spec of the unit you're about to buy rather than trusting a comparison table
(this one included): the category is moving fast enough that revisions add and drop
features between batches.

PicoKVM's firmware is open-sourced Buildroot with a mainline-ish kernel and U-Boot, but
some Rockchip drivers, media libraries and flashing tools stay proprietary — "open
source" here means most of the stack, not all of it.

## Treat it as the most privileged device you own

Think about what this box holds: keyboard input, screen output, the power button, and the
ability to boot the host off media it supplies. Anyone who gets into your IP KVM doesn't
get *access to* the machine — they get the machine, underneath the operating system,
below anything the OS could log or prevent.

Independent audits through 2026 found the cheap end of the market riddled with basic
flaws. Sipeed's NanoKVM is the documented case, and it's worth reading as a category
lesson rather than as one vendor's bad week:

- Early firmware shipped **default credentials with SSH enabled**
- The key encrypting login passwords was **hardcoded and identical on every device**, with
  no salting or key derivation
- No CSRF protection and no way to invalidate a session
- DNS routed through Chinese servers by default; routine calls home to fetch a
  closed-source binary, with the verifying key stored in plaintext and **no firmware
  integrity check**
- An **undocumented microphone** on the board
- **CVE-2026-32296**, an unauthenticated Wi-Fi configuration endpoint allowing an
  auth bypass — fixed in firmware 2.3.1

Sipeed has addressed much of this, and the point isn't to single them out. It's that this
device class was consumerised faster than its security was, so:

1. **Never port-forward an IP KVM.** Not "with a strong password", not "on a weird
   port". Never.
2. Put it on a **management VLAN** and reach it through
   [Tailscale](/docs/network/tailscale/) or WireGuard.
3. Change every default credential before it touches the network, and update the firmware
   first, not eventually.
4. Avoid unbranded ultra-cheap clones entirely, and keep the bargain units out of
   professional environments.

## Automating it, carefully

PiKVM exposes an HTTP API, including ATX power management — read the current power state,
change it, or send a button-press event — which is how you fold "power-cycle the lab box"
into a script, a monitoring alert, or a deploy pipeline. `pikvm-lib` wraps the same thing
in Python, covering ATX, mass-storage images and GPIO.

That API is also the seam where an agent can reach physical hardware: an MCP server or a
few tool definitions over those endpoints, and "restart the build machine" becomes a
sentence rather than a trip.

Before you build that, put a gate in it. A hallucinated power cycle against a running
database is not a funny demo — it's data loss with no undo, and the model has no way to
know the box was mid-write. Keep destructive verbs behind an explicit human confirmation,
allowlist which machines the agent may touch at all, and log every call. Read-only status
queries are the safe half; make them the default and let the rest ask permission.

## Next

You've been through every category. To start again, pick another from the
[overview](/docs/).
