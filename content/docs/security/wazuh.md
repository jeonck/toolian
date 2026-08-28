---
weight: 8750
title: "Wazuh"
description: "Open-source SIEM and host security monitoring — agents on your machines, one place to see what happened, and compliance reports you didn't hand-write."
icon: "gpp_good"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Everything else in this category runs at a moment in time: a scan, a commit, a build. The
question none of them answer is *what happened on that server last night* — who logged in,
which file changed, what process started, and whether the pattern is normal.

[Wazuh](https://wazuh.com/) is the open-source answer: an agent on each host that watches
logs, file integrity, processes, and configuration, reporting into a central server with a
dashboard, alerting, and compliance mapping. It's a SIEM you can run without a licence
negotiation.

## The shape of it

| Piece | Does |
|---|---|
| **Agent** | Runs on each Linux, Windows, macOS host, container host, or cloud VM |
| **Server** | Analyses events against rules and decoders, raises alerts |
| **Indexer** | Stores events (OpenSearch) for search and retention |
| **Dashboard** | Search, dashboards, compliance views |

```bash
curl -sO https://packages.wazuh.com/4.x/wazuh-install.sh
sudo bash ./wazuh-install.sh -a          # all-in-one server, indexer, dashboard
```

Then enrol a host:

```bash
curl -sO https://packages.wazuh.com/4.x/wazuh-agent.deb
sudo WAZUH_MANAGER='10.0.0.5' dpkg -i ./wazuh-agent.deb
sudo systemctl enable --now wazuh-agent
```

Budget properly: the indexer is OpenSearch, so this wants real memory and disk, and
retention is a decision with a cost attached. This is infrastructure, not a utility.

## What it actually catches

- **File integrity monitoring.** `/etc`, `/usr/bin`, your application directory — a change
  you didn't deploy is a question worth asking, and this is the requirement most
  compliance frameworks name explicitly.
- **Authentication events.** Failed SSH bursts, a first-time login for an account, sudo to
  root at 3am.
- **Rootkit and anomaly checks**, plus process and port audits.
- **Log analysis with decoders** for the software you already run — web servers, databases,
  cloud audit logs — so events arrive parsed rather than as text.
- **Vulnerability detection**, by matching installed package inventories against CVE feeds.
  Useful for the long-lived hosts that [Trivy](/docs/devops/trivy/) never sees because
  they aren't images.
- **Compliance mapping** — PCI DSS, CIS benchmarks, GDPR-relevant controls — pre-mapped to
  rules, which turns an audit request from a week of screenshots into a report.

## Getting value instead of noise

The failure mode is identical to every other detection tool, and worth stating plainly:
an untuned deployment produces thousands of alerts a day and teaches everyone to ignore
the dashboard.

1. Run for two weeks with **nobody paged**. Read the top alert types.
2. For each, decide: normal here (tune it), interesting but not urgent (dashboard only), or
   genuinely wake-someone (route it).
3. Only then wire anything to a channel with an expectation of response.
4. Revisit quarterly — a rule set that made sense at fifty hosts doesn't at five hundred.

## Where it sits next to the rest

[Falco](/docs/devops/falco/) watches syscalls inside Kubernetes and is sharper there.
Wazuh covers the **hosts** — including the ones nobody containerised, which are usually
the ones nobody is watching. Plenty of places run both and forward Falco's events into
Wazuh so there's one place to look.

If you have no long-lived servers at all — everything is containers on a managed platform
— Wazuh is likely more than you need; your cloud's audit log plus Falco covers the ground.
The moment you own machines, the calculation changes.

## Next

The system is watched. Now automate your own work →
[Automation & Shortcuts](/docs/automation/)
