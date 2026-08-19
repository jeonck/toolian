---
weight: 7010
title: "curl"
description: "The HTTP client that exists everywhere. Only the flags you actually use."
icon: "cable"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

`curl` is already installed on virtually every server, which makes it the first thing
you reach for when confirming "it works on my laptop but not on the box."

## Basics

```bash
curl https://api.example.com/users            # GET, body only
curl -i https://api.example.com/users         # include response headers
curl -I https://api.example.com/users         # headers only (HEAD)
curl -s https://api.example.com/users | jq    # quiet, piped into jq
curl -o users.json https://api.example.com/users   # save to a file
```

## Flags worth knowing

| Flag | Meaning |
|---|---|
| `-X POST` | Set the method |
| `-H "Key: value"` | Add a header (repeatable) |
| `-d '{"a":1}'` | Send a body (implies POST) |
| `--data-urlencode` | URL-encode a form value |
| `-F file=@a.png` | Multipart file upload |
| `-u user:pass` | Basic auth |
| `-L` | Follow redirects |
| `-s` / `-S` | Hide progress / still show errors |
| `-f` | Fail the exit code on HTTP errors (essential in scripts) |
| `-k` | Skip certificate verification (debugging only) |
| `--max-time 10` | Overall timeout in seconds |
| `-v` | Print the whole request and response exchange |

## Calling a JSON API

```bash
curl -sS -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"name":"Alex Kim","email":"alex@example.com"}' | jq
```

A long body reads better from a file:

```bash
curl -sS -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -d @payload.json | jq
```

## Debugging

```bash
# see the full exchange
curl -v https://api.example.com/health

# just the status code
curl -s -o /dev/null -w "%{http_code}\n" https://example.com

# where the time actually goes
curl -s -o /dev/null -w \
  "dns:%{time_namelookup} connect:%{time_connect} tls:%{time_appconnect} total:%{time_total}\n" \
  https://example.com
```

That last line tells you in one shot whether a slow API is DNS, the TLS handshake, or
the server.

## Safely in scripts

```bash
set -euo pipefail
curl -fsS --max-time 10 --retry 3 --retry-delay 2 \
  -H "Authorization: Bearer $TOKEN" \
  https://api.example.com/health
```

Without `-f`, a 404 exits 0 and the failure goes unnoticed. Always include it in
automation.

## Reproducing a browser request

Chrome DevTools → Network → right-click the request → `Copy as cURL`. Pasting it
reproduces the headers and cookies exactly, which is the fastest way to debug an API
behind authentication.

**Careful**: what you copied contains session cookies and tokens. Strip them before
pasting into an issue or a chat.

## Next

For syntax that's easier to read and write → [HTTPie](/docs/network/httpie/)
