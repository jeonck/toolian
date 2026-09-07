---
weight: 5130
title: "Camofox Browser"
description: "A browser server for agents: accessibility snapshots instead of HTML, stable element refs, and a Firefox build that ordinary bot detection doesn't reject."
icon: "travel_explore"
date: "2026-09-07"
lastmod: "2026-09-07"
draft: false
---

Point an agent at a real website and two things go wrong. The page arrives as 400 KB of
HTML that burns the context window before the agent has read anything useful, and the
site's bot detection turns the request away before that. [Camofox
Browser](https://github.com/jo-inc/camofox-browser) is a REST server that addresses both:
it wraps [Camoufox](https://camoufox.com) — a Firefox fork that spoofs fingerprint
surfaces at the C++ level rather than patching them from JavaScript — and exposes it
through an API shaped for agents.

## Run it

```bash
npx @askjo/camofox-browser        # first run downloads Camoufox, ~300 MB
# -> http://localhost:9377
```

Docker is a `make up` away; `railway.toml` and Fly.io deploys are included. Idle memory
sits around 40 MB because the browser launches lazily and shuts down when unused, so it
can share a $5 VPS with everything else. MIT licensed. Interactive API docs are at
`/docs`, and the OpenAPI spec at `/openapi.json`.

As an OpenClaw plugin:

```bash
openclaw plugins install @askjo/camofox-browser
```

That gives the agent `camofox_create_tab`, `camofox_snapshot`, `camofox_click`,
`camofox_type`, `camofox_navigate`, `camofox_scroll`, `camofox_screenshot`,
`camofox_list_tabs`, `camofox_close_tab`, and `camofox_import_cookies`.

## The part that actually matters: snapshots and refs

The API returns an **accessibility snapshot** rather than the DOM — roughly 90% smaller
than raw HTML — with every interactive element carrying a stable ref (`e1`, `e2`, `e3`).
The agent clicks `e7`; it never writes a CSS selector that breaks when a class name
changes.

`POST /tabs/:tabId/extract` takes a JSON Schema whose properties map to those refs via
`x-ref`, so "get me the title, price, and rating" comes back as structured JSON instead
of prose the agent has to re-parse.

Long pages truncate with offset-based pagination, screenshots can ride along with the
snapshot as base64 PNG, and there are macros — `@google_search`, `@youtube_search`,
`@amazon_search`, `@reddit_subreddit`, and about ten more — for the searches every agent
ends up doing. A `/youtube/transcript` endpoint pulls captions via `yt-dlp` with no API
key.

## Sessions, cookies, and logins

Each user gets an isolated session, persisted to `~/.camofox/profiles/`, so an
authenticated state survives a restart. There are two ways to get logged in:

- **Cookie import.** Export a Netscape-format cookie file, drop it in
  `~/.camofox/cookies/`, and the agent imports it. This is **off unless you set
  `CAMOFOX_API_KEY`** — without the key the server rejects every cookie request with a
  403.
- **VNC login.** Log in visually through noVNC, then export the storage state for the
  agent to reuse.

```bash
export CAMOFOX_API_KEY="$(openssl rand -hex 32)"
```

Keep that key in your shell profile, systemd unit, or platform secrets — not in
`openclaw.json`, which stores plugin config in plaintext.

## Before you deploy it

- **Telemetry is on by default.** Anonymised crash and hang reports are filed as GitHub
  issues, with private domains HMAC-hashed and paths, tokens, and IPs stripped. Turn it
  off with `CAMOFOX_CRASH_REPORT_ENABLED=false` if any of that is a problem for you.
- **Imported cookies are live credentials on disk.** A session file that logs into your
  LinkedIn is worth as much as the password. Treat that directory accordingly, and give
  the agent an account you're willing to lose.
- **Anti-detection is not authorisation.** Getting past a fingerprint check says nothing
  about whether you're allowed to be there. Terms of service, rate limits, robots.txt,
  and the law where you and the site live all still apply, and "the browser could" is not
  a defence any of them accept. Automating *your own* account, a site you operate, or
  public data you're permitted to collect is the ground this belongs on.
- **If there's an API, use the API.** A browser is the fallback for sites that don't
  offer one — slower, more fragile, and more likely to break next Tuesday.

## Where it fits

| Want | Reach for |
|---|---|
| An agent that browses arbitrary sites on a server | Camofox Browser |
| Driving *your* logged-in browser on your own machine | A browser extension or Playwright directly |
| Search results a model can read | [Exa](/docs/ai/exa/) |
| Authenticated access to specific applications | [Composio](/docs/ai/composio/) |

Camofox is infrastructure, not an agent: it gives one hands on the open web, and leaves
the judgement about where to put them to you.

## Next

Changes made with AI need version control around them →
[Git & Collaboration](/docs/git/)
