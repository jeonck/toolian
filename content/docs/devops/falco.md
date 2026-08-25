---
weight: 8220
title: "Falco"
description: "Runtime threat detection — watching what containers actually do, after every scan has passed."
icon: "notification_important"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

[Trivy](/docs/devops/trivy/) checks what you're about to ship. [Policy as code](/docs/devops/policy-as-code/)
checks what the cluster will accept. Neither notices the container that passed both and
then, at 4am, opened a shell and started reading `/etc/shadow`.

[Falco](https://falco.org/) watches the running system. It taps kernel events — syscalls,
container lifecycle, Kubernetes audit logs — and matches them against rules describing
behaviour that shouldn't happen. CNCF graduated, originally from Sysdig.

## Install

```bash
helm repo add falcosecurity https://falcosecurity.github.io/charts
helm install falco falcosecurity/falco \
  --namespace falco --create-namespace \
  --set tty=true
```

It runs as a DaemonSet — one agent per node — and ships with a default ruleset that is
genuinely useful before you write anything.

## What a rule looks like

```yaml
- rule: Shell spawned in a container
  desc: A shell was started inside a running container
  condition: >
    container.id != host and proc.name in (bash, sh, zsh)
    and not container.image.repository in (allowed_debug_images)
  output: >
    Shell in container (user=%user.name container=%container.name
    image=%container.image.repository cmd=%proc.cmdline)
  priority: WARNING
  tags: [container, shell]
```

The default rules already cover the behaviour that matters most: a shell in a container,
a write to a system binary directory, reading sensitive files, an unexpected outbound
connection, a container running with new privileges, a change to the container's package
manager state.

## The work is tuning, not installing

Out of the box Falco is noisy, because a lot of legitimate software looks alarming. The
first two weeks are the job:

1. **Send everything to a low-priority channel** and read it daily.
2. For each recurring alert, decide: is this normal here? Add a macro or an exception with
   a comment saying *why* — never disable a whole rule because one deployment trips it.
3. Only once the noise floor is near zero, wire high-priority rules to a channel people
   are expected to answer.

An untuned Falco produces the same outcome as no Falco, with more Slack messages.

```yaml
# a scoped exception beats deleting the rule
- list: allowed_debug_images
  items: [my-registry/toolbox, my-registry/migrate-job]
```

## Getting the alerts somewhere useful

Falcosidekick forwards events to Slack, PagerDuty, Loki, S3, or a webhook, and it's the
usual companion install:

```bash
helm install falco falcosecurity/falco \
  --set falcosidekick.enabled=true \
  --set falcosidekick.config.slack.webhookurl=$SLACK_WEBHOOK
```

Sending events into [Loki](/docs/observability/loki/) alongside your application logs is
the pairing worth setting up — during an incident you want the detection and the
application's own output on one screen.

## What it does and doesn't give you

It **detects**; it does not block. Response is a separate decision — a human, or an
automated action through Falco Talon or your own webhook consumer. Blocking on a
false positive in production is its own outage, so most teams stay in detect-and-page
mode for a long time, and that's a defensible place to stop.

Where it earns its keep: a compromised dependency behaving oddly, a misused debug
container, crypto-mining in a forgotten namespace, and the audit requirement that
something is watching runtime behaviour at all.

## Next

Once there are many services and many teams, someone has to find them all →
[Backstage](/docs/devops/backstage/)
