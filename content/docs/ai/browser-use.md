---
weight: 5125
title: "Browser Use"
description: "An agent that opens pages, clicks, types, and fills forms — the library most of the AI-browsing world is built on."
icon: "ads_click"
date: "2026-09-07"
lastmod: "2026-09-07"
draft: false
---

[Exa](/docs/ai/exa/) gives an agent search results. [Browser
Use](https://github.com/browser-use/browser-use) gives it the browser: it opens the page,
reads what's on it, clicks the button, types in the field, and submits the form — the
same way you would. You describe the task; it does the steps.

It's MIT-licensed, Python 3.11+, and the most widely used project of its kind by a wide
margin.

## Two ways in, and they're for different jobs

**You already run an agent** — Claude Code, Codex, Cursor. Install the skill and it can
drive a browser:

```bash
uv add browser-use          # or: pip install browser-use
browser-use skill install
```

Then ask in plain language: compare these three laptops into a table, upload this video,
fill in this application.

**You're writing software that automates the web** — use the library:

```python
import asyncio
from browser_use import Agent, ChatBrowserUse

async def main():
    agent = Agent(
        task="Find the number of stars of the browser-use repo",
        llm=ChatBrowserUse(model='openai/gpt-5.5'),
        # llm=ChatAnthropic(model='claude-opus-4-8'), or any provider you like
    )
    await agent.run()

asyncio.run(main())
```

The project's own rule of thumb is the right one: **one-off tasks through an agent → the
CLI; repeatable automation in code → the library.** Scheduled scraping, monitoring, QA
runs, or a browser agent embedded in your product all belong in the second column.

Custom tools extend what the agent can do beyond clicking:

```python
from browser_use import Tools

tools = Tools()

@tools.action(description='Look up an order in our admin API.')
def lookup_order(order_id: str) -> str:
    ...

agent = Agent(task="...", llm=llm, tools=tools)
```

## Free, and also a product

The open-source agent runs on your machine with any LLM — including local models through
[Ollama](/docs/ai/ollama/) — and costs nothing beyond inference. There's also a hosted
cloud version that adds proxy rotation, stealth, captcha solving, integrations, and
persistent state, billed per run.

Be aware of which numbers you're reading. The accuracy comparison in the README is the
project's own [benchmark](https://github.com/browser-use/benchmark) — open source, which
is more than most vendors offer — and it favours the hosted agent, because that's the
product. The independent data point is the Odysseys leaderboard, where it places first on
long-horizon web tasks. Either way, the free local agent is a real tool, not a demo.

## What it actually feels like to run

- **Every step is an LLM call.** A ten-step task is ten or more round trips: slower than
  a script and priced per token. Fine for a task you'd otherwise do by hand, expensive as
  a scraper you run hourly. Once a flow is stable, consider recording it rather than
  re-deciding it every run.
- **It is non-deterministic.** The same task can take a different path twice. That's the
  point — it survives a redesign that would break a CSS selector — but it also means you
  cannot assume the last run's steps.
- **Logins are the hard part.** Anything with 2FA, a captcha, or a bot check will stop
  it. That's where a [browser server built for the job](/docs/ai/camofox/) comes in.
- **Give it an account you can afford to lose,** and don't let it near irreversible
  actions — payments, deletions, "send to all contacts" — without a human confirming.
  An agent that misreads a page will click the wrong button confidently.
- **A site's terms still apply.** Automating your own account is ordinary; scraping at
  volume, or evading a bot check to get at something you're not permitted to have, is a
  different question with a different answer.

## Browser Use or Camofox

| | Browser Use | [Camofox](/docs/ai/camofox/) |
|---|---|---|
| Is | A complete agent loop — decides what to click | A browser server — exposes the page as tools |
| You bring | A task and an LLM | An agent that already exists |
| Strength | Doing the whole task end to end | Token-efficient snapshots, stable refs, sessions |
| Blocked by | Bot detection, unless you add a stealth browser | Much less, by design |

They compose: Browser Use can drive a remote browser, and a hardened one is exactly what
you want underneath it for anything public-facing.

## Next

For the browser layer underneath, built to survive bot detection →
[Camofox Browser](/docs/ai/camofox/)
