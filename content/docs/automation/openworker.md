---
weight: 9080
title: "OpenWorker"
description: "Andrew Ng's desktop AI coworker — you ask for an outcome, it works your real tools, and a three-tier governance model decides what it can do without asking first."
icon: "engineering"
date: "2026-09-24"
lastmod: "2026-09-24"
draft: false
---

[n8n](/docs/automation/n8n/) and [Flowise](/docs/automation/flowise/) automate by having
you draw the flow. [OpenWorker](https://github.com/andrewyng/openworker) skips the
canvas: you ask for an outcome — "prepare a customer brief," "check where the release
stands across Jira and GitHub" — and it plans the steps, works your actual tools, and
hands back a finished document instead of a chat reply.

It's Andrew Ng's project, open source, MIT licensed, in open beta as of July 2026.

## Install

Signed, notarized, auto-updating builds for macOS on Apple Silicon; a Windows build that
works but isn't code-signed yet, so expect a SmartScreen warning.

```
macOS:   https://download.openworker.com/mac
Windows: https://download.openworker.com/windows
```

Open it, add a model key or point it at [Ollama](/docs/ai/ollama/), ask for something
real. Running from source needs Python 3.10+, Node 20+, and Rust for the desktop shell:

```bash
git clone https://github.com/andrewyng/openworker
cd openworker
bash packaging/setup_dev_env.sh                            # one-time bootstrap
.venv/bin/openworker-server --cwd ~/some/project --port 8765
cd surfaces/gui && npm install && npm run tauri dev         # full desktop app
```

The architecture is a Tauri desktop shell over a local Python agent server, built on
[aisuite](https://github.com/andrewyng/aisuite) — Ng's own unified LLM API library, with
OpenWorker as the reference app for what it can carry.

## Use cases

Coworkers arrive pre-configured with the tools and working style for one job. Security
ships first:

- **Security review** — scans a codebase and its dependencies with deterministic scanners
  (semgrep and friends) plus model reasoning, proposes fixes, then **re-scans and
  diff-reviews the fix before you approve it.** The model that wrote the fix is never the
  only thing checking it.
- **Cloud posture** — audits configuration against common misconfiguration classes and
  drafts the remediation plan.
- **Incident triage** — pulls context across your tools, drafts the timeline, prepares
  the report while the incident is still live.
- **Everyday work** — a customer call prepped from CRM and inbox, scattered notes turned
  into a shippable plan, documents and spreadsheets that land as files, calendar and
  Slack threads kept current.
- **Standing automations** — a morning brief, a weekly report, a watch over a channel,
  on a schedule, with a full transcript of every run.

It also answers from inside Slack: `@OpenWorker` in a channel opens a session on your
desktop, the work happens with your real tools, and the reply comes back in the thread —
so the request doesn't have to start at the desktop app at all.

## The governance model is the reason to trust it with real tools

This is the part worth reading closely, because it's the actual engineering answer to
"how much do you let an agent do unsupervised." Three tiers, all enforced in the repo
rather than by prompting:

1. **Hard floors.** A set of dangerous, irreversible operations is human-only, always —
   no setting, including full auto-approve, lowers them.
2. **A ladder of earned autonomy.** Actions are approval-gated by default. A one-off
   approval can graduate into a standing rule, then into a config allowlist — each step
   explicit and revocable. In auto-approve mode, a **separate reviewer model** lets
   routine actions through and escalates anything it isn't confident about; repeated
   denials trip a circuit breaker that pauses the reviewer and hands control back to you.
3. **An audit trail.** Every tool call is logged with its approval provenance —
   auto-approved, user-approved, or denied, with the reviewer's reasoning attached — and
   kept with the conversation.

The framing that matters: **reviewer verdicts are judgments, not guarantees — the floors
and the audit trail are the actual backstop.** A scheduled run never self-approves past
that; unattended jobs park their asks in an inbox until a human answers, so "runs on a
schedule" doesn't quietly become "runs unsupervised."

For anyone who read the [IP KVM](/docs/self-hosted/ip-kvm/) page's warning about gating
destructive actions on an agent-driven system, this is what a real implementation of that
gate looks like.

## Bring your own model

No lock-in: OpenAI, Anthropic, Google Gemini, BytePlus Ark, Inkling, GLM, DeepSeek, Kimi,
Qwen, MiniMax, Mistral, Grok, plus open-weight models through Together and Fireworks, or
fully local through Ollama. A curated list marks what's actually verified for
tool-calling; anything else works at your own risk.

25+ connectors ship built in — GitHub, Slack, Jira, Notion, Linear, HubSpot, Outlook,
monday.com, Gmail, Google Calendar — plus your terminal and local files, plus any
[MCP](https://modelcontextprotocol.io/) server, with per-tool control over what's exposed.

## Privacy, concretely

Local-first: the agent loop, conversation history, connector tokens and model keys all
live in the app's local secret store. **The one cloud piece is a small service that
brokers OAuth handshakes for connectors** — everything else stays on your machine, and
you can skip even that by wiring connectors with manually created API keys instead of
signing in.

## Next

To write down what you automated → [Linux Desktop](/docs/linux-desktop/)
