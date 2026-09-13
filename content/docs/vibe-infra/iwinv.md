---
weight: 11180
title: "iwinv"
description: "A Korean cloud with a Seoul data centre, ₩5,600/month VMs, GPU boxes, a real CLI, and an MCP server you can drive from Claude Code."
icon: "dns"
date: "2026-09-13"
lastmod: "2026-09-13"
draft: false
---

Everything else in this category is a US or EU platform. [iwinv](https://www.iwinv.kr/)
is the odd one out: a Korean IaaS run by Smileserv since 2002, out of its own data
centre in Gasan, Seoul. If your users are in Korea, that's a 5ms round trip instead of
150ms to Tokyo or Oregon — and the bill comes in won, from a company you can phone.

It's not a "vibe" platform in the Vercel sense. It's plain VMs, bare metal, GPU servers,
block and object storage, load balancers, and managed MySQL/PostgreSQL/MongoDB. What
earns it a page here is that all of it has an API, a CLI, and an **MCP server**, so an
AI agent can stand the infrastructure up for you.

## What it costs

| Thing | Price (2026) |
|---|---|
| Smallest VM, `vgna_1_n` — 1 vCPU, 1 GB, 25 GB NVMe | ₩5,600/month (~$4) |
| V100 GPU server | ₩349,000/month |
| RTX 4090 GPU server | ₩495,000/month |
| RTX PRO 6000 GPU server | ₩2,375,000/month |
| New accounts | A 3-month free trial event (check the current terms) |

Billing is metered hourly, so a server you create and delete in an afternoon costs an
afternoon. Prices above are the monthly ceiling.

## The CLI

```bash
curl -sL https://cli.iwinv.kr/install.sh | bash     # Linux / macOS
```

Create an API key pair in the [console](https://console.iwinv.kr/), then:

```bash
iwinv login          # prompts for Access Key / Secret Key; endpoint api-kr.iwinv.kr
iwinv account        # confirm the profile

iwinv zones
iwinv images
iwinv flavors --type GENERAL --max-mem 4096
iwinv ssh-keys

iwinv instances create
iwinv instances show
iwinv instances vnc        # a browser console URL for when SSH is broken
iwinv instances delete
```

Flavor names encode the tier: `vgna_*` is shared general purpose, `gna_*` and `hpa_*`
are dedicated, `soi_*` is storage-optimised. `iwinv flavors -h` lists the filters
(`--type GPU`, `--min-gpu 1`, and so on).

`iwinv bill` prints the current charges, which is the command to run before you forget
about a GPU box.

## The MCP server

This is the interesting part. `https://mcp.iwinv.kr` is a remote MCP server with
OAuth login, so any MCP client can manage your account in plain language.

```bash
claude mcp add --transport http iwinv https://mcp.iwinv.kr
claude mcp list
```

A browser opens for the iwinv login; after that, "spin up a 2-core Ubuntu box in Seoul
and add my SSH key" is a sentence rather than a console session. Cursor, Windsurf, VS
Code, Codex, and Gemini CLI are documented too, and in Claude.ai it's a custom connector
with the same URL.

Treat it like any agent with a credit card: create and delete are both one sentence
away. Keep a separate API key for the agent, and run `iwinv instances show` yourself
before you trust "done."

## Where it fits

| Good fit | Poor fit |
|---|---|
| Korean users, Korean latency, KRW invoices | A global audience — use [Fly.io](/docs/vibe-infra/fly-io/) |
| A cheap always-on VM for a side project | Push-to-deploy with zero ops — [Railway](/docs/vibe-infra/railway/) |
| Renting a 4090 by the hour for a fine-tune | Anything that needs a managed serverless stack |
| Teams that want a support desk in Korean | Anyone allergic to SSH and a Linux box |

It replaces the *hosting* slot for a Korea-first product. The rest of the stack —
[Postgres](/docs/vibe-infra/postgres/) via their managed DB or your own on the VM,
[Cloudflare R2](/docs/vibe-infra/cloudflare-r2/) or their object storage,
[Sentry](/docs/vibe-infra/sentry/) — still applies.

## Next

One category left, on the assets a project needs and has no budget for →
[AI Media Tools](/docs/ai-media/)
