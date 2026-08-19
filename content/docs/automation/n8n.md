---
weight: 9050
title: "n8n"
description: "A self-hostable automation tool that wires services and APIs together as nodes."
icon: "account_tree"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

"When a GitHub issue opens, post to Slack and add a row in Notion" — n8n builds that
kind of glue without code. It resembles Zapier and Make, with the difference that
**you can host it yourself so the data never leaves.**

## Run it with Docker

```bash
docker run -d --name n8n \
  -p 5678:5678 \
  -v n8n_data:/home/node/.n8n \
  -e GENERIC_TIMEZONE="Asia/Seoul" \
  -e TZ="Asia/Seoul" \
  docker.n8n.io/n8nio/n8n
```

Create an account at `http://localhost:5678` and start building.

Managed with Compose:

```yaml
services:
  n8n:
    image: docker.n8n.io/n8nio/n8n
    ports:
      - "5678:5678"
    environment:
      - GENERIC_TIMEZONE=Asia/Seoul
      - N8N_HOST=n8n.example.com
      - WEBHOOK_URL=https://n8n.example.com/
    volumes:
      - n8n_data:/home/node/.n8n
    restart: unless-stopped

volumes:
  n8n_data:
```

## Concepts

| Element | Meaning |
|---|---|
| **Workflow** | One automation made of connected nodes |
| **Trigger node** | The starting point: a schedule, a webhook, an app event |
| **Regular node** | Calls an app, transforms data, branches |
| **Execution** | A record of one run, with the data at each step |

## Workflows worth building

1. **Tidying error alerts**: receive errors on a webhook → de-duplicate → route to Slack
   channels by severity
2. **Daily report**: 9 a.m. trigger → query the database → format a table → send an email
3. **Issue triage**: GitHub issue created → classify the body with an LLM node → apply
   labels
4. **Backup verification**: list an S3 bucket → alert if yesterday's file is missing

## Where code comes in

A Code node lets you shape data with JavaScript.

```javascript
// tidy up the incoming items
return items.map(item => ({
  json: {
    title: item.json.title.trim(),
    url: item.json.html_url,
    createdAt: new Date(item.json.created_at).toISOString(),
  }
}));
```

## Self-hosting checklist

- **HTTPS**: receiving webhooks needs a public address. Put it behind a reverse proxy
  (Caddy, Nginx) or a Cloudflare Tunnel.
- **Authentication**: always enable basic auth or SSO. Workflows hold a lot of API keys.
- **Backups**: workflows and credentials live in the `n8n_data` volume. Back it up.
- **Upgrades**: node behaviour can change, so run your important workflows once after
  updating.

## When to use it, when to write code

| Situation | Choice |
|---|---|
| Wiring three or four services simply | n8n |
| Complex branching that needs tests | Code plus CI |
| Non-engineers need to edit it | n8n |
| Hundreds of events per second | Code (a dedicated worker) |

## Next

To write down what you automated → [Docs & Notes](/docs/writing/)
