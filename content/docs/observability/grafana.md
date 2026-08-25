---
weight: 8520
title: "Grafana"
description: "Dashboards over whatever you already store — and the discipline that keeps them useful after the first week."
icon: "dashboard"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

[Grafana](https://grafana.com/oss/grafana/) is the query-and-draw layer. It doesn't store
anything itself: you point it at [Prometheus](/docs/observability/prometheus/),
[Loki](/docs/observability/loki/), Postgres, or a dozen other sources, and it renders
panels, tables, and alerts over them. AGPL, free to self-host, and there's a free cloud
tier if you'd rather not run it.

```bash
docker run -d -p 3000:3000 grafana/grafana-oss
```

Log in at `localhost:3000` (`admin` / `admin`), add a data source, and you're at the
part that actually matters: what to put on the screen.

## Import before you build

```
Dashboards → New → Import → paste a dashboard ID from grafana.com/dashboards
```

Node Exporter Full, Kubernetes cluster views, Postgres, nginx — the common ones are
already built and better than a first attempt. Import, use for a week, then delete the
panels you never looked at. That subtraction is what turns someone else's dashboard into
yours.

## One dashboard people actually open

The failure mode is a wall of forty panels nobody reads. A service dashboard that earns
its place has one screen, and answers three questions in this order:

1. **Is it serving?** Request rate and error ratio, as big numbers with a threshold
   colour.
2. **Is it slow?** p95 and p99 latency over time, not the average — the average hides
   exactly the users who are suffering.
3. **Is it about to stop?** Saturation: CPU, memory, connection pool, queue depth.

Everything else goes on a second dashboard for the person debugging, linked from the
first.

Two features worth setting up once: **variables** (a `$service` dropdown so one dashboard
serves every service, rather than forty copies) and **annotations** (deploy markers
overlaid on the graphs — "it got slow at 14:02" and "we deployed at 14:01" is the fastest
root cause you will ever find).

## Alerting

Grafana can own alerting across data sources, which is the reason to use it rather than
Prometheus rules if your signals live in several places. The rules are the same either
way:

- Alert on **symptoms users feel** — errors, latency, unavailability — not on causes like
  CPU at 80%, which is often fine.
- Every alert carries a runbook link and a named owner.
- If an alert fires and nobody does anything, delete it or fix the threshold. Silenced
  alerts are worse than none, because they look like coverage.

## Keep dashboards in Git

Clicking a dashboard together is fine; leaving it only in the database is not. Export the
JSON and commit it, or provision from files:

```yaml
# provisioning/dashboards/main.yml
apiVersion: 1
providers:
  - name: default
    folder: Services
    type: file
    options:
      path: /var/lib/grafana/dashboards
```

Now a dashboard change is reviewable, and a rebuilt Grafana comes back with everything.

## Next

Graphs tell you when; logs tell you what → [Loki](/docs/observability/loki/)
