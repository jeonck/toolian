---
weight: 8530
title: "Loki"
description: "Searchable logs without a full-text index — cheap to run, and queried by the same labels as your metrics."
icon: "receipt_long"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Logs are the signal everyone already has and nobody can search. They're on a machine, in
a container that has since been replaced, or in an Elasticsearch cluster that costs more
than the application it watches.

[Loki](https://grafana.com/oss/loki/) takes a deliberately cheaper approach: **index the
labels, not the content**. Log lines are compressed and stored in object storage; only a
small set of labels — `app`, `namespace`, `level` — is indexed. Queries narrow by label
first, then grep the matching chunks. Less power than a full-text index, a fraction of
the cost, and the labels are the same ones your metrics use.

## Run it

```bash
docker run -d -p 3100:3100 grafana/loki
docker run -d -v /var/log:/var/log grafana/promtail
```

On Kubernetes, `grafana/loki-stack` via [Helm](/docs/devops/helm/) installs Loki plus a
collector that picks up every pod's stdout automatically. Then add Loki as a data source
in [Grafana](/docs/observability/grafana/) — that's where you'll query it.

Shipping logs is a job for an agent: Promtail (or Grafana Alloy, its successor) tails
files and container output and attaches labels. Applications keep writing to stdout,
which they should be doing anyway.

## LogQL

It reads like PromQL with a grep bolted on, because that's what it is.

```logql
{app="api"}                                    # everything from one app
{app="api"} |= "timeout"                       # containing a string
{app="api"} | json | status >= 500             # parse JSON, filter on a field
{app="api"} != "healthcheck" |~ "user_id=\\d+" # exclude, then regex

# and metrics derived from logs
sum(rate({app="api"} |= "error" [5m])) by (namespace)
```

That last query is the trick worth knowing: Loki can turn a log stream into a graph, so
you get an error-rate chart from logs alone, on the same screen as Prometheus metrics,
before anyone instruments anything.

## Two rules that decide whether this works

**Label with low-cardinality values only.** `app`, `env`, `level`, `namespace` — yes. A
user ID, a request ID, or a trace ID as a *label* is how you recreate the expensive
system you were escaping. Put those inside the log line, where filters can still find
them.

**Log structured JSON.** `| json` then gives you real fields to filter on, and a message
that a human can read is worth less than a line a query can dissect. One line per event,
with a level, a timestamp, and the identifiers you'll want to search by — including the
trace ID from [OpenTelemetry](/docs/observability/opentelemetry/), which is what lets you
jump from a slow trace straight to its logs.

## Retention, which is the real decision

Logs grow forever unless you decide otherwise. Set retention per stream — thirty days for
application logs, longer for audit trails — and put chunks in S3 or
[R2](/docs/vibe-infra/cloudflare-r2/) rather than local disk. The cost of Loki is almost
entirely the storage you chose to keep.

## Next

Metrics say when, logs say what; only traces say *where* →
[OpenTelemetry](/docs/observability/opentelemetry/)
