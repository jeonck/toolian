---
weight: 8510
title: "Prometheus"
description: "Scraping numbers off your services and querying them over time — the default metrics database, and PromQL."
icon: "monitor_heart"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

[Prometheus](https://prometheus.io/) is a time-series database that **pulls**: you expose
a plain-text endpoint of current numbers, it scrapes that endpoint every few seconds and
keeps the history. No agent to configure, no push credentials to distribute, and a
service that can't be scraped is itself a signal.

It's a CNCF graduated project and the de facto standard — if a tool exports metrics at
all, it exports them in this format.

## Run one

```yaml
# prometheus.yml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: my-app
    static_configs:
      - targets: ['app:8080']      # must serve /metrics
```

```bash
docker run -p 9090:9090 -v $PWD/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
```

Open `http://localhost:9090`, and **Status → Targets** tells you immediately whether the
scrape works. On Kubernetes, install kube-prometheus-stack with
[Helm](/docs/devops/helm/) and you get Prometheus, Alertmanager, Grafana, and the node
and cluster metrics in one chart.

## Instrument your app

Every language has a client library; the shape is the same everywhere.

```python
from prometheus_client import Counter, Histogram, start_http_server

REQUESTS = Counter("http_requests_total", "Requests", ["method", "status"])
LATENCY = Histogram("http_request_duration_seconds", "Duration", ["endpoint"])

REQUESTS.labels(method="GET", status="200").inc()
with LATENCY.labels(endpoint="/api/items").time():
    handle_request()

start_http_server(8080)          # serves /metrics
```

Four metric types, and you'll use two: **Counter** (only goes up — requests, errors) and
**Histogram** (a distribution — latency, sizes). Gauges are for things that go up and
down, like queue depth; summaries are best avoided unless you know why you want one.

**Never label with something unbounded.** User ID, request ID, full URL path with
parameters — each distinct value creates a new time series, and cardinality is what kills
a Prometheus. `endpoint="/api/items"` is fine; `endpoint="/api/items/48213"` is a bill.

## PromQL, the five queries that cover most days

```promql
# request rate per second, last 5 minutes
sum(rate(http_requests_total[5m])) by (job)

# error ratio
sum(rate(http_requests_total{status=~"5.."}[5m]))
  / sum(rate(http_requests_total[5m]))

# 95th percentile latency
histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))

# memory per pod
sum(container_memory_working_set_bytes) by (pod)

# is it up?
up{job="my-app"}
```

`rate()` on a counter is the one to internalise: counters only rise, so the useful
question is always *how fast*, never *how many since the process started*.

## Alerts

```yaml
groups:
  - name: app
    rules:
      - alert: HighErrorRate
        expr: |
          sum(rate(http_requests_total{status=~"5.."}[5m]))
            / sum(rate(http_requests_total[5m])) > 0.05
        for: 10m
        labels: { severity: page }
        annotations:
          summary: "5xx above 5% for 10 minutes"
          runbook: "https://wiki.example.com/runbooks/high-error-rate"
```

`for:` is what separates an alert from a nuisance — the condition must hold, not just
flicker. Alertmanager handles the routing, grouping, and silences; point it at Slack,
email, or a pager.

## What it isn't

Prometheus is not long-term storage and not a log store. Local retention is weeks, not
years — for longer history, remote-write to Thanos, Mimir, or a hosted backend. Recent
versions also accept OTLP directly, so an [OpenTelemetry](/docs/observability/opentelemetry/)
pipeline can write metrics here without a separate exporter.

## Next

Numbers need a face → [Grafana](/docs/observability/grafana/)
