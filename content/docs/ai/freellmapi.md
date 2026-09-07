---
weight: 5048
title: "FreeLLMAPI"
description: "Stacking thirty-four providers' free tiers behind one OpenAI-compatible endpoint — with the Docker and Podman startup that actually works."
icon: "call_split"
date: "2026-09-07"
lastmod: "2026-09-07"
draft: false
---

Every AI lab hands out a free tier. Individually each one is a toy — a few thousand
requests a day, a quota you hit by lunchtime.
[FreeLLMAPI](https://github.com/tashfeenahmed/freellmapi) stacks 34 of them behind a
single OpenAI-compatible endpoint with routing and failover, so your client points at
`http://localhost:3001/v1` and stops caring which provider answered.

Google, Groq, Cerebras, Mistral, OpenRouter, Cloudflare, Cohere, Z.ai, NVIDIA,
HuggingFace and more — plus a custom provider for any OpenAI-compatible endpoint, which
is how you fold in [Ollama](/docs/ai/ollama/), llama.cpp, or vLLM alongside them. MIT
licensed, local-first, keys encrypted at rest in SQLite.

## Docker

```bash
git clone https://github.com/tashfeenahmed/freellmapi.git
cd freellmapi
printf "ENCRYPTION_KEY=%s\nPORT=3001\n" "$(openssl rand -hex 32)" > .env
docker compose up -d
docker compose logs -f freellmapi
```

There's also a one-liner installer (`curl -fsSL https://freellmapi.co/install.sh | bash`)
and desktop builds for macOS and Windows, where the dashboard signs itself in and there's
no password to set.

Compose binds the port to `127.0.0.1` on purpose. To reach it from another machine on a
trusted LAN:

```bash
HOST_BIND=0.0.0.0 docker compose up -d
```

## Podman

The image runs the same way, with two differences that will bite you if you skip them.

```bash
podman run -d \
  --name freellmapi \
  -p 127.0.0.1:3001:3001 \
  -e ENCRYPTION_KEY="$(openssl rand -hex 32)" \
  -v freellmapi_data:/app/data:Z \
  ghcr.io/tashfeenahmed/freellmapi:latest
```

**`ENCRYPTION_KEY` is not optional.** The image runs in production mode, and without a
64-character hex key the server exits immediately — the container is "running" while the
port answers nothing:

```
[server] Failed to start:
  ENCRYPTION_KEY is required in production for API key encryption.
```

Generate one with `openssl rand -hex 32`, or `node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"`.
Keep it: the stored keys are AES-256-GCM, so changing it later doesn't re-encrypt them —
there's a `rotate-encryption-key` script for that. If you'd rather not put the key in your
shell history, export it first and pass the bare name:

```bash
export ENCRYPTION_KEY="…"
podman run -d --name freellmapi -p 127.0.0.1:3001:3001 \
  -e ENCRYPTION_KEY -v freellmapi_data:/app/data:Z \
  ghcr.io/tashfeenahmed/freellmapi:latest
```

**`:Z` on the volume** relabels it for SELinux. On Fedora, RHEL, and derivatives, leaving
it off gives you permission errors on the database that look like the app is broken.

Note the `127.0.0.1:` prefix on the published port: plain `-p 3001:3001` binds every
interface, and this service holds your provider keys and answers requests without
authentication on the API side. Bind it to localhost unless you specifically want it on
the LAN.

## When port 3001 doesn't answer

In order, cheapest first:

```bash
podman logs freellmapi      # nine times out of ten the answer is here
podman port freellmapi      # expect 3001/tcp -> 127.0.0.1:3001
podman ps -a                # is it actually still up, or did it exit?
```

Two specific messages worth recognising:

- **`ENCRYPTION_KEY is required in production`** — see above.
- **"A setup code is required to create the first account from a remote device."** You're
  opening the dashboard from a different machine than the one running the container. The
  code is printed in the server log, so `podman logs freellmapi` and paste it into the
  form. Opening `http://localhost:3001` on the host itself skips this.

Forgotten a password later? The one-time reset code goes to the log too, and lasts 15
minutes — there's no email to send it to.

## Starting it at boot

The command in most guides still works but is deprecated in current Podman:

```bash
podman generate systemd --name freellmapi --files --new
mv container-freellmapi.service ~/.config/systemd/user/
systemctl --user enable --now container-freellmapi.service
```

The supported way now is a **Quadlet** unit —
`~/.config/containers/systemd/freellmapi.container`:

```ini
[Container]
ContainerName=freellmapi
Image=ghcr.io/tashfeenahmed/freellmapi:latest
PublishPort=127.0.0.1:3001:3001
Volume=freellmapi_data:/app/data:Z
EnvironmentFile=%h/.config/freellmapi.env

[Service]
Restart=always

[Install]
WantedBy=default.target
```

```bash
systemctl --user daemon-reload
systemctl --user start freellmapi
loginctl enable-linger "$USER"     # so it survives you logging out
```

Put `ENCRYPTION_KEY=…` in `~/.config/freellmapi.env` with `chmod 600`.

## Pointing your tools at it

Add provider keys on the **Keys** page, then take the unified key from that page's header.
Anything that accepts an OpenAI base URL works — Claude Code, Codex CLI, Cline, Continue,
Aider, opencode, Cursor — and there are generators for the config files:

```bash
npx freellmapi setup-claude --url http://localhost:3001 --api-key <unified-key>
```

## "So is it actually free?"

You don't need every provider — **one key is enough to start**, and the obvious first
choices are Google AI Studio and Groq, whose free tiers are generous and take two minutes
to get. Nothing is billed, because you're using each lab's own free allowance. The custom
provider also lets you point at a local model that needs no key at all.

What it costs you instead is honesty about the limits, and the project states them
plainly:

- **No frontier models, variable latency, no SLA.** The good models hit their daily caps,
  so the endpoint gets noticeably less capable late in the day and resets at UTC midnight.
- **It's for personal experimentation, not production.** Free tiers exist for prototyping.
  If you ship something real, put a paid API behind it.
- **Each provider's terms still apply to you** when the traffic is proxied. The repository
  documents a provider-by-provider review; read it before you route anything commercial
  through it.

## Next

An agent with no subscription and no API key, and the trade that pays for it →
[Freebuff](/docs/ai/freebuff/)
