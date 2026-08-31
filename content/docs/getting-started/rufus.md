---
weight: 1010
title: "Rufus"
description: "Writing a bootable USB stick on Windows — fast, and with the installer customisations that save an hour of clicking."
icon: "usb"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Before any of the rest of this site applies, something has to put an operating system on
the machine. That means a bootable USB stick, and on Windows the tool for it is
[Rufus](https://rufus.ie/): a single portable executable, about a megabyte and a half,
GPL-licensed, that writes an ISO to a drive faster than anything else on the platform.

It is also the tool that decides how much of the Windows installer you have to argue with,
which is the part people underestimate.

## Write a drive

1. Download the `.exe` from **rufus.ie** and run it. There's nothing to install.
2. Plug in the USB stick and select it under **Device**. Check the letter twice — the
   drive is erased.
3. **Select** the ISO. (Rufus can also download Windows ISOs for you, from Microsoft's own
   servers.)
4. Leave **Partition scheme** on GPT and **Target system** on UEFI for anything from the
   last decade; switch to MBR/BIOS only for genuinely old hardware.
5. **Start**, and pick *Write in ISO Image mode* if it asks. DD mode is for images that
   demand it — some Linux distributions say so explicitly.

Five minutes, and it boots.

## The Windows 11 dialogue that saves an hour

When you write a Windows 11 ISO, Rufus offers a customisation panel before it starts. The
options that matter:

| Option | What it avoids |
|---|---|
| Remove requirement for TPM 2.0, Secure Boot, and 8 GB RAM | The "This PC can't run Windows 11" wall on older hardware |
| Remove the Microsoft account requirement | Being forced to sign in to create a local account |
| Disable data collection | The privacy questions during setup |
| Set the regional options from this machine | The keyboard and locale interview |

These are genuinely useful for a spare laptop or a lab machine. Two honest caveats:

- **A bypassed install is an unsupported install.** Microsoft's position is that updates
  aren't guaranteed on hardware below the requirements. For a machine that matters, this
  is a decision, not a default.
- **These options are a moving target.** Microsoft tightens the requirements, Rufus
  adapts, and which bypasses work depends on the ISO build and the Rufus version. Use a
  current Rufus with the ISO you actually intend to install.

## Other things it does well

- **Persistent partition** for a Linux live USB — files and installed packages survive a
  reboot, which turns a live stick into a usable rescue environment.
- **Bad blocks check**, worth running once on a cheap or ancient USB stick before you
  trust an install to it.
- **UEFI:NTFS support**, so a single install file larger than 4 GB — every modern Windows
  ISO — works without a FAT32 workaround.

## When something else fits better

| Situation | Use |
|---|---|
| Windows host, one ISO at a time | Rufus |
| Many ISOs on one stick, boot menu to choose | **Ventoy** — copy ISO files onto the drive; no rewrite per image |
| macOS or Linux host | `dd`, or balenaEtcher for a GUI |
| Verifying the download first | `sha256sum` / `certutil -hashfile` against the vendor's published hash |

That last row isn't optional. A tampered ISO installs a compromised operating system, and
nothing you do afterwards recovers from it — check the checksum, from the vendor's site,
before you write.

Ventoy deserves the look if you keep more than one image around: one prepared stick, then
ISOs are just files you copy on and off.

## Next

The machine boots. Now decide what deserves to be installed on it →
[Choosing a Tool](/docs/getting-started/choosing-tools/)
