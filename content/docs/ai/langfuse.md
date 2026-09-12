---
weight: 5180
title: "Langfuse"
description: "Self-hosted tracing and evaluation for LLM apps — every call, cost and score on a timeline you own, MIT licensed and uncapped."
icon: "timeline"
date: "2026-09-12"
lastmod: "2026-09-12"
draft: false
---

A test suite tells you what broke before release. It tells you nothing about the prompt a
real user sent at 3am that made your agent call the same tool eleven times.

[Langfuse](https://github.com/langfuse/langfuse) records that: every LLM call, chain step,
tool invocation, token count and cost, on a trace you can open and read. Then it lets you
attach **scores** to those traces — from an LLM judge, from a human reviewer, or from user
feedback — which is what turns a log viewer into an evaluation system.

## Self-hosting it

```bash
git clone https://github.com/langfuse/langfuse.git
cd langfuse
docker compose up
```

That brings up six containers, and it's worth knowing why before you size a box: the web
UI, an async worker, **PostgreSQL** for metadata, **ClickHouse** for trace analytics,
**Redis** for queueing, and **S3-compatible blob storage** (MinIO locally). Moving traces
out of Postgres into a columnar store is what lets aggregations run over hundreds of
thousands of traces in milliseconds.

One design detail worth copying in your own systems: **events land in blob storage
first**, and are only written to the database after successful processing. If the database
is briefly down, nothing is lost — the events replay.

For production, Helm on Kubernetes is the preferred path, with Terraform templates for
AWS, Azure and GCP.

## Getting data in

Python and JS/TS SDKs for manual instrumentation, a drop-in OpenAI SDK replacement,
callback handlers for LangChain and LlamaIndex, Haystack tracing, and LiteLLM — which
covers roughly everything else by proxy. If you already emit OpenTelemetry, that works
too.

## The licence, and the acquisition

The core is **MIT** — with the honest asterisk that the `ee/` folders are not. Self-hosting
carries no licence cost and no event cap; the cloud tiers meter, self-hosting doesn't.

**ClickHouse acquired Langfuse in January 2026.** The core stayed MIT and self-hosting is
unchanged and actively maintained — unsurprising given ClickHouse was already the storage
engine underneath. Still, note the pattern on this and the [promptfoo](/docs/ai/promptfoo/)
page: the LLM tooling layer consolidated hard in 2026, and "open source" is a licence,
not a promise about direction.

## The alternative worth comparing against

**Arize Phoenix** is the other obvious self-host option, and the difference that decides it
isn't features:

| | Langfuse | Arize Phoenix |
|---|---|---|
| Licence | MIT core (`ee/` excepted) | Elastic License 2.0 — source-available, not OSI |
| Start cost | Six containers | `pip install arize-phoenix`, one process |
| Shape | Production platform: prompt management, scaling | Developer-first toolkit, OpenTelemetry-native |

ELv2 forbids offering Phoenix as a hosted service to third parties. For internal use that
changes nothing; if your product *is* an observability service, it changes everything.
Start with Phoenix for the lightest possible setup, move to Langfuse when you want prompt
management and an architecture that scales.

## Next

Changes made with AI need version control around them →
[Git & Collaboration](/docs/git/)
