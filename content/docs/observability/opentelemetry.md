---
weight: 8540
title: "OpenTelemetry"
description: "One vendor-neutral way to emit traces, metrics, and logs — instrument once, and change backends without touching the code."
icon: "sensors"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

The old bargain with monitoring vendors was that instrumentation was theirs: their SDK
in your code, their format on the wire, and a rewrite the day you wanted to leave.
[OpenTelemetry](https://opentelemetry.io/) — CNCF, and the second-most-active open-source
project after Kubernetes — breaks that. You instrument against a standard API, and where
the data goes is a config file.

That is the whole pitch, and it's enough: **instrument once, switch backends later.**

## Three pieces

| Piece | What it does |
|---|---|
| **API and SDK** | What your code calls, per language |
| **Collector** | A process that receives, processes, and forwards telemetry |
| **OTLP** | The wire protocol everything speaks |

The Collector is the part that pays off soonest. Applications send to it; it batches,
strips attributes you don't want to store, samples, and fans out to as many backends as
you like. Changing vendor becomes an exporter change in one YAML file, not a redeploy of
forty services.

```yaml
receivers:
  otlp:
    protocols: { grpc: {}, http: {} }

processors:
  batch: {}
  memory_limiter: { check_interval: 1s, limit_percentage: 80 }

exporters:
  otlphttp/traces:
    endpoint: http://tempo:4318
  prometheus:
    endpoint: 0.0.0.0:8889

service:
  pipelines:
    traces:  { receivers: [otlp], processors: [memory_limiter, batch], exporters: [otlphttp/traces] }
    metrics: { receivers: [otlp], processors: [memory_limiter, batch], exporters: [prometheus] }
```

## Get traces without writing instrumentation

Auto-instrumentation covers the frameworks and clients you already use — HTTP servers,
database drivers, queue clients — which is most of what you want to see.

```bash
# Python
pip install opentelemetry-distro opentelemetry-exporter-otlp
opentelemetry-bootstrap -a install
OTEL_SERVICE_NAME=api OTEL_EXPORTER_OTLP_ENDPOINT=http://collector:4318 \
  opentelemetry-instrument python app.py
```

```bash
# Node
npm install @opentelemetry/auto-instrumentations-node
node --require @opentelemetry/auto-instrumentations-node/register app.js
```

Then add spans by hand only where the automatic ones don't explain enough:

```python
from opentelemetry import trace
tracer = trace.get_tracer(__name__)

with tracer.start_as_current_span("price-basket") as span:
    span.set_attribute("basket.items", len(items))
    total = compute(items)
```

## Why traces change debugging

A metric says the endpoint got slower. A trace says *this* request spent 40ms in your
code, 900ms waiting on the recommendations service, and 30ms in the database — and shows
you the same for the request that was fast. With more than one service, that is the
difference between a bisect and a look.

Two things make traces worth keeping:

- **Context propagation across every hop.** The trace ID must travel through HTTP calls
  and queue messages, or your trace ends at the service boundary. Auto-instrumentation
  handles the common cases; custom transports need attention.
- **Trace IDs in your logs.** Put the trace ID on every log line and you can jump from a
  slow span straight to what it printed — the reason to pair this with
  [Loki](/docs/observability/loki/).

## Sampling, before the bill arrives

Tracing every request at volume is expensive and mostly redundant. Head sampling keeps a
fixed fraction and is trivial; tail sampling, in the Collector, decides *after* the trace
completes — keep every error and everything slow, sample the boring successes at 1%.
That combination keeps the traces you'd actually open.

## Where to send it

Traces need a backend: Grafana Tempo, Jaeger, or a hosted one. Metrics can go straight to
[Prometheus](/docs/observability/prometheus/), which now accepts OTLP directly. The point
of the standard is that this list can change without your code changing.

## Next

You know what the system does under today's load. What about tomorrow's? →
[k6](/docs/observability/k6/)
