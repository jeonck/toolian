---
weight: 7020
title: "HTTPie"
description: "A human-friendly HTTP client. Especially terse for JSON work."
icon: "http"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

HTTPie does the same job as curl with shorter syntax and output that is coloured and
formatted automatically. Poking at a JSON API by hand takes less than half the typing.

## Install

```bash
brew install httpie
pip install httpie
sudo apt install httpie
```

## Basics

```bash
http GET https://api.example.com/users
http https://api.example.com/users          # GET is the default
https example.com/users                     # shorthand for the https scheme
```

Responses are syntax-highlighted and JSON is formatted, so you can read them without
piping through `jq`.

## The syntax rules

| Symbol | Meaning | Example |
|---|---|---|
| `=` | JSON string field | `name=Alex` |
| `:=` | JSON raw type (number, boolean, array) | `age:=30` `tags:='["a","b"]'` |
| `:` | Header | `Authorization:"Bearer $TOKEN"` |
| `==` | Query string | `page==2` |
| `@` | File upload | `avatar@./me.png` |
| `=@` | File contents as a string field | `bio=@bio.txt` |

## Side by side with curl

```bash
# curl
curl -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"name":"Alex","age":30,"active":true}'

# HTTPie
http POST api.example.com/users \
  Authorization:"Bearer $TOKEN" \
  name=Alex age:=30 active:=true
```

`Content-Type: application/json` is added for you.

## Flags worth knowing

| Flag | Meaning |
|---|---|
| `-v` | Show the full request and response |
| `-h` | Response headers only |
| `-b` | Response body only |
| `--follow` | Follow redirects |
| `-d` | Download to a file |
| `-a user:pass` | Basic auth |
| `--session=name` | Persist cookies and headers in a session |
| `--offline` | Print the request without sending it |

## Sessions for authentication

```bash
http --session=prod POST api.example.com/login username=me password=secret
http --session=prod GET api.example.com/me      # cookies persist
```

Session files live in `~/.config/httpie/sessions/`. Passwords can end up there in plain
text, so be careful on shared machines.

## When to use curl instead

| Situation | Tool |
|---|---|
| Exploring an API on your own machine | HTTPie |
| Inside a production server or container | curl (already there) |
| CI scripts and health checks | curl (`-f` exit-code handling) |
| Examples in documentation | curl (universally available) |

## Next

To save requests and share them with a team →
[Bruno and Postman](/docs/network/bruno-postman/)
