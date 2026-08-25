---
weight: 85
title: "Observability & Reliability"
description: "Metrics, dashboards, logs, traces, and load tests — knowing what the system is doing before a user tells you."
icon: "monitoring"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Deploying is the easy half. The hard half is the question you get at 9am: *is it slow
for everyone, or just for them, and since when?* Without instrumentation the honest
answer is a shrug, and the debugging that follows is guesswork with a deploy button.

This category is the open-source stack that answers it, in the order you'd adopt it.

| Page | Answers |
|---|---|
| [Prometheus](/docs/observability/prometheus/) | How much, how many, how often — numbers over time |
| [Grafana](/docs/observability/grafana/) | What the numbers look like, and when to wake someone |
| [Loki](/docs/observability/loki/) | What the logs said around that spike |
| [OpenTelemetry](/docs/observability/opentelemetry/) | Where a single slow request spent its time |
| [k6](/docs/observability/k6/) | What happens before it breaks, on your terms rather than a customer's |

## The three signals, and the one you should add first

**Metrics** are cheap, aggregated, and answer "how much" — start here. **Logs** are
detailed and expensive at volume; you already have them, so the work is making them
searchable. **Traces** show one request's path across services and are what you want the
moment there is more than one service.

Start with metrics on the four things every service has — request rate, error rate,
duration, and saturation — plus one alert on each user-facing symptom. That's an
afternoon, and it catches most of what a full stack would.

## A caution about what this costs

Every tool here is free to run and none of them are free to operate. Storage grows,
dashboards multiply, and an alert that fires without a human action attached teaches
people to ignore alerts. Two rules keep it healthy: **cardinality is the bill** — a label
holding user IDs or request IDs turns one metric into millions — and **every alert names
a symptom and a next step**, or it gets deleted.

Application errors have their own tool with less setup: [Sentry](/docs/vibe-infra/sentry/)
catches exceptions with stack traces, and pairs well with everything here.
