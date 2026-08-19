---
weight: 7040
title: "Exposing a Local Server"
description: "Turning localhost into a temporary public URL for webhook testing and phone checks."
icon: "swap_horiz"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Sometimes you need to receive a payment provider's webhook, or look at the screen you're
building on an actual phone. These tools open `localhost:3000` to the world for a while
without deploying anything.

## ngrok

The most widely used. Needs an account and an auth token.

```bash
brew install ngrok
ngrok config add-authtoken <token>
ngrok http 3000
```

It prints a URL like `https://xxxx.ngrok-free.app`. Put that in your webhook settings.

At `http://localhost:4040` you can see every request that came through, headers and body
included, and **replay any of them**. That replay is the single most useful thing here
for webhook debugging — you can reproduce the same event without making another payment.

## cloudflared

Needs only a Cloudflare account, and quick tunnels work without even logging in.

```bash
brew install cloudflared
cloudflared tunnel --url http://localhost:3000
```

For a stable address, create a named tunnel.

```bash
cloudflared tunnel login
cloudflared tunnel create dev
cloudflared tunnel route dns dev dev.example.com
cloudflared tunnel run dev
```

## SSH reverse tunnel

If you already have a server, you need nothing else.

```bash
ssh -R 8080:localhost:3000 user@myserver.com
```

The server's `sshd_config` needs `GatewayPorts yes` for outside access.

## Comparison

| | ngrok | cloudflared | SSH reverse |
|---|---|---|---|
| Prerequisite | Account | Cloudflare account (for stable URLs) | A server with a public IP |
| Request inspector | Yes, and it's good | No | No |
| Stable domain | Paid | Free with your own domain | Your server's domain |
| Setup effort | Lowest | Medium | Medium |

## Security notes

Opening a tunnel means **the entire internet can reach your local server.**

- Don't keep real customer data in the development database.
- Check that debug endpoints and admin pages aren't exposed.
- Open it only for as long as you need and close it. Don't leave one running in the
  background.
- ngrok supports `--basic-auth "user:pass"` for a minimal guard.
- URLs you share persist in logs after expiry. Keep sensitive paths out of them.

## Next

On to shipping → [Containers & Deploys](/docs/devops/)
