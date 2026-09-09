---
weight: 9070
title: "Ddalkkak Threads Factory"
description: "Drafting Threads posts with a logged-in Claude Code CLI and publishing them on a schedule through Meta's official API — locally, on Windows, with the safety switch on by default."
icon: "schedule_send"
date: "2026-09-09"
lastmod: "2026-09-09"
draft: false
---

Most "post to social automatically" tools are a browser being driven by a script, which
is how they end up locked out. [Ddalkkak Threads Factory
](https://github.com/apache3563-bit/ddalkkak-threads-community) (딸깍 Threads 공장) takes
the boring route instead: it drafts posts with the **Claude Code CLI already logged in on
your machine**, and publishes them through **Meta's official Threads Graph API** on a
`node-cron` schedule. No Playwright, no Selenium, no CDP, no cookies, no session
stealing — and, deliberately, nothing that looks like evasion.

The Community edition landed as
[v1.11.7](https://github.com/apache3563-bit/ddalkkak-threads-community/releases/tag/v1.11.7)
on 2026-09-08 under AGPL-3.0. It's a Windows 10/11 local web app: an Express server that
binds only to `127.0.0.1`/`::1` on port **3970**, a SQLite file for state, and a plain
HTML/CSS/JS dashboard at `http://localhost:3970`.

## The pipeline

| Stage | What happens |
|---|---|
| Collect | RSS feeds, URLs you paste, raw text, keywords/topics |
| Draft | `claude -p --output-format json --max-turns 2`, prompt over stdin |
| Review | Edit and approve in the drafts box |
| Schedule | Pick a time; cron checks every minute (Asia/Seoul) |
| Publish | Text, image, video, or a 2–3 card carousel via `graph.threads.net` |

Persona ("말투") settings apply to every generation, so the drafts come out in one voice
rather than in whatever register the model felt like that morning.

Note what's **not** in the collect stage: it doesn't scrape Threads, Instagram, Facebook,
or X. Public RSS, URLs you supply, and text you paste — that's the whole input surface,
and URLs you type in are run through an SSRF filter that blocks localhost and private
ranges.

## Install

```bat
:: Node.js 22+ required — better-sqlite3 won't build below it
install.bat     :: npm ci --ignore-scripts, npm run build, copies .env.example → .env
start.bat       :: then open http://localhost:3970
```

There's also a signed installer on the release page
(`DdalkkakThreadsFactory_Community_Setup_1.11.7.exe`, with a published SHA-256) if you'd
rather not have a toolchain.

The Community edition ships **without the vendor's relay server configuration**, which is
the meaningful difference from the paid build: you bring your own Meta developer app, and
if you want the optional relays you deploy the Cloudflare Workers in `workers/` yourself.

## Getting a token

This is the part that takes an afternoon, and no blog post's screenshots will match the
current dashboard — follow
[Meta's Threads docs](https://developers.facebook.com/docs/threads).

1. Create an app at `developers.facebook.com/apps` and add the **Threads use case**. This
   issues a *Threads* app ID and secret, separate from the ordinary Facebook ones. Use
   the Threads pair.
2. **App roles → Roles → Add People → Threads Tester**, invite your own account, then
   accept the invite in Threads under **Settings → Website permissions**.
3. Through the Authorization Window, consent to `threads_basic` and
   `threads_content_publish`, then trade the code up: authorization code (1 hour) →
   short-lived token (1 hour) → long-lived token (60 days).

```bash
# short-lived → long-lived
curl "https://graph.threads.net/access_token?grant_type=th_exchange_token\
&client_secret=<THREADS_APP_SECRET>&access_token=<SHORT_LIVED>"

# your user ID
curl "https://graph.threads.net/v1.0/me?fields=id,username&access_token=<TOKEN>"
```

Both go in `.env`, never in the web UI — the dashboard only ever shows the first and last
four characters. Long-lived tokens expire at 60 days and can be renewed with
`refresh_access_token` once they're more than 24 hours old. Put a calendar reminder at day
50; "publishing silently stopped" is almost always this.

## Two safeties, and they're both on

```ini
DRY_RUN=true            # default: log "would have published", touch nothing
DAILY_MAX_PUBLISH=6     # against an official ceiling of ~250
```

`DRY_RUN` writes a `[DRY_RUN]` line to the publish log and moves the draft to
`dry_run_done` instead of `published`. Separately, the dashboard's **auto-publish toggle
starts off** — with it off the scheduler queries and then does nothing at all, not even a
dry run. So a first-time setup fails closed twice, and the checklist to actually go live
is: valid token, "verify token" passing in the UI, `DRY_RUN=false`, server restarted, and
the post in `scheduled` state (a `draft` never publishes, ever).

Handy consequence: `DRY_RUN` doesn't check for a token at all, so you can exercise the
whole collect → draft → schedule loop before you've been anywhere near
`developers.facebook.com`.

## Publishing media is where people get hurt

Meta's API doesn't accept an upload — it fetches a **public URL** you hand it. This
program is local-only, so your files aren't reachable, and image/video publishing fails
loudly with `THREADS_MEDIA_PUBLIC_URL_REQUIRED` rather than quietly degrading to text.

The fix is a second, dedicated port:

```ini
THREADS_MEDIA_GATEWAY_PORT=<a port that serves only /threads-media/:token>
THREADS_MEDIA_PUBLIC_BASE_URL=https://<tunnel or proxy pointing at that port>
THREADS_MEDIA_TOKEN_TTL_MS=1800000
```

**Do not tunnel port 3970.** That's the whole dashboard and its database API. The gateway
port exists precisely because it serves one route and nothing else, behind single-use
tokens with a 30-minute default lifetime. If your tunnel points at the main port you've
put your drafts, your settings, and a token-shaped hole on the public internet.

## When it doesn't work

| Symptom | Cause |
|---|---|
| `better-sqlite3` fails to install | Node below 22, or missing "Desktop development with C++" build tools |
| Dashboard says Claude Code: disconnected | Run `claude --version` in a **new** terminal — PATH isn't refreshed in old ones |
| Generation fails on JSON parsing | The raw reply is saved to `logs/claude-failure-*.txt`; read it, then retry |
| "Auth error (not retrying)" in the log | Expired or under-scoped token. It deliberately does not retry — refresh it |
| Still only `[DRY_RUN]` lines | `DRY_RUN=false` saved, but the server not restarted |
| Port 3970 in use | Change `PORT` in `.env` |

Claude Code here needs a Pro/Max/Team/Enterprise login; the free claude.ai plan won't
drive the CLI. The program never asks for an Anthropic API key, and it never gives the
model file-write or command-execution permission — it asks for post JSON and nothing
else.

## Running it unattended

Task Scheduler, triggered "at log on", pointed at `start.bat` — and set **Start in** to
the project folder, or the relative paths (`.env`, `./data/app.db`) resolve somewhere
else and you get a fresh, empty database. A shortcut in `shell:startup` works too. Either
way the obvious limit applies: it's your PC, so a scheduled post at 09:00 needs the
machine awake at 09:00.

State lives in `data/app.db`, uploads in `data/uploads/`, logs in `logs/`, and the token
under `%APPDATA%\DdalkkakThreadsFactoryCommunity\threads\`. Back up the first three; the
`.gitignore` already keeps all of them out of a commit, which is worth verifying yourself
before you push a fork.

## Next

To write down what you automated → [Linux Desktop](/docs/linux-desktop/)
