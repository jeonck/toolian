---
weight: 5050
title: "Freebuff"
description: "A coding agent with no subscription, no credits, and no API key — funded by ads, which is the part to read carefully."
icon: "auto_awesome_motion"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Every agent on the previous pages eventually asks for money — a subscription, credits, or
your own API key with a bill attached. [Freebuff](https://freebuff.com/) doesn't. You
install it, run it, and the models are included; **text ads in the session** pay for the
inference.

It comes from the team behind Codebuff, whose open multi-agent framework provides the
orchestration underneath, and it is a real agent rather than a demo: file finding,
implementation, review, research, and browser testing are handled by **separate
specialised agents** instead of one model doing everything through one prompt.

## Run it

```bash
npm install -g freebuff
cd ~/my-project
freebuff
```

No account, no `.env`, no key to paste. There are also desktop apps for macOS, Windows,
and Linux, a web builder, a cloud agent with GitHub integration, and a chat interface —
the CLI is the one that fits the rest of this site.

The included models are a rotating set of open and commercial ones, chosen for you rather
than configured; the desktop app can also drive an existing Claude or Codex account if you
have one. Access can be metered in some regions — a few hour-long sessions a day rather
than unlimited — so check what applies where you are before you plan a working day around
it.

## Read this before pointing it at work code

The trade is not hidden, and it is not nothing: **your prompts pay for the service**.
Messages support ad personalisation, and some models and features state that submissions
may be used for training. That's the deal ads always are, and it lands differently
depending on the repository.

| Code | Reasonable? |
|---|---|
| A side project, a prototype, learning | Yes — this is what it's for |
| Open source you already publish | Yes |
| A client's codebase under NDA | No — check the contract first, and assume the answer is no |
| Your employer's proprietary code | Ask, don't assume. Many policies forbid exactly this |

That is the same question every hosted assistant raises; it's sharper here because the
business model runs on the content rather than on a subscription. Read the current privacy
terms rather than this page — they're the thing that decides it.

## Where it fits

For personal projects and learning, a capable multi-agent assistant at zero cost is a
genuinely good deal, and the free-tier ceiling that usually interrupts an afternoon isn't
there. For anything under a confidentiality obligation, [Claude Code](/docs/ai/claude-code/)
or another paid tool with an enterprise agreement is the boring correct answer — and
[Ollama](/docs/ai/ollama/) remains the option where nothing leaves the machine at all.

| Want | Use |
|---|---|
| A capable agent, no bill, non-sensitive code | Freebuff |
| A paid agent with a data-handling agreement | [Claude Code](/docs/ai/claude-code/), Copilot, Cursor |
| Nothing leaving your machine | [Ollama](/docs/ai/ollama/) with a local model |

Whatever you run, the habits from the other pages still apply: commit before you start,
read the diff, and give the agent a way to verify its own work.

## Next

Whichever agent you use, it works better with a map of the repository →
[Graft](/docs/ai/graft/)
