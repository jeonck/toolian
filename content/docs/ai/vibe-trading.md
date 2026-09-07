---
weight: 5140
title: "Vibe-Trading"
description: "An open-source research workspace where finance questions become runnable backtests — 90 skills, agent teams, and an audit trail."
icon: "insights"
date: "2026-09-07"
lastmod: "2026-09-07"
draft: false
---

The video tools earlier in this category showed the skills-plus-agent pattern applied to
one domain. [Vibe-Trading](https://github.com/HKUDS/Vibe-Trading) is the same idea aimed
at markets: ask a question in plain language, and it pulls data, writes strategy code,
runs a backtest, and hands back a report you can archive. It's from HKU's data science
lab, MIT-licensed, and holds no funds of its own.

## Try it

```bash
pip install vibe-trading-ai
vibe-trading init                  # interactive .env setup
vibe-trading run -p "Backtest a BTC-USDT 20/50 moving-average strategy for 2024, \
  summarize return and drawdown, then export the report"
```

Three commands come with the package:

| Command | What it starts |
|---|---|
| `vibe-trading` | The interactive CLI |
| `vibe-trading serve --port 8899` | FastAPI backend and web UI |
| `vibe-trading-mcp` | An MCP server, so your existing agent gets the tools |

Python 3.11+, or `docker compose up --build` for the zero-setup path. It needs an LLM
key from one of about twenty providers — or [Ollama](/docs/ai/ollama/) locally, no key at
all. Market data works with no keys either: Yahoo, OKX, mootdx, and AKShare cover
equities, crypto, futures, and forex through an automatic fallback chain.

## What's actually in the box

- **90 skills across 9 categories** — the finance equivalent of the specialist subagents
  earlier in this category. They're editable Markdown, and yours override the bundled
  ones by filename.
- **Agent teams** — investment, quant, crypto, macro, and risk workers that research in
  parallel and persist their reports.
- **An alpha zoo of 462 factors** (Qlib, Kakushadze, GTJA-191, academic, PIT-safe
  fundamentals), benchable in one line:

```bash
vibe-trading alpha bench --zoo gtja191 --universe csi300 --period 2018-2025 --top 20
```

- **Cross-market backtesting** — A-shares, HK, US, Canada, UK, India, Korea, plus crypto,
  futures, and forex, with composite runs and run cards.
- **Shadow Account** — the most interesting feature and the one nobody else has. Upload a
  broker export; it profiles your actual behaviour (holding days, win rate, disposition
  effect, overtrading, anchoring), extracts the rules you *actually* trade by, backtests
  those rules, and shows where you broke them. It audits you, not a strategy.
- **Exports that leave the tool** — reports, TradingView Pine Script, MetaTrader 5, TDX.

## Read this part before the fun part

- **This is not investment advice, and the project says so itself.** It's research
  software. Past performance doesn't guarantee anything, and an agent that writes a
  strategy has no idea whether the edge is real.
- **Live trading is experimental.** Broker execution runs only through a channel you
  authorise and within limits you set, and you can halt it — but the maintainers state
  plainly that it is **not verified against a real broker account**. Paper-trade it.
- **A backtest an agent generated is a hypothesis, not a result.** Look-ahead bias,
  survivorship, and overfitting to a period are exactly the failures a fluent report is
  best at hiding. The project puts real work into guarding these — PIT data, strict
  out-of-sample gates, a hash-chained audit ledger over every run — which is a reason to
  take it seriously, not a reason to skip the scrutiny.
- **There is no Vibe-Trading token.** The repository carries a standing warning that an
  X account, a Virtuals project, and a token contract using its name are impersonations.
  The project has never launched or endorsed a coin. Don't connect a wallet to anything
  claiming otherwise.

## Where it fits

| Want | Reach for |
|---|---|
| To research an idea and see it backtested end to end | Vibe-Trading |
| To understand your own trading habits | Its Shadow Account, on your broker export |
| To hand market tools to an agent you already run | `vibe-trading-mcp` |
| Someone to tell you what to buy | Nothing here. That's not what this is |

The honest framing: it's a research workspace that removes the plumbing between a
question and a tested answer. What it can't remove is the judgement about whether the
answer means anything.

## Next

Changes made with AI need version control around them →
[Git & Collaboration](/docs/git/)
