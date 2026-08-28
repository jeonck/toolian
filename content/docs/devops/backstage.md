---
weight: 8250
title: "Backstage"
description: "An internal developer portal — a catalogue of every service, who owns it, and the templates for making the next one."
icon: "corporate_fare"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

At around thirty services, three questions start costing real time: *what services exist,
who owns this one, and how do I create a new one that follows our conventions?* The
answers live in a wiki that's out of date, in a spreadsheet nobody updates, and in one
senior engineer's head.

[Backstage](https://backstage.io/) — Spotify's, donated to the CNCF — is a portal that
answers them from metadata kept next to the code. It's a framework rather than a product:
you build and host your own instance, which is both its strength and its cost.

## Three things it does

**A software catalogue.** Every service, library, and data pipeline described by a small
YAML file in its own repository:

```yaml
# catalog-info.yaml
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: payments-api
  description: Charges, refunds, and webhook delivery
  annotations:
    github.com/project-slug: my-org/payments-api
    backstage.io/techdocs-ref: dir:.
spec:
  type: service
  lifecycle: production
  owner: team-payments
  dependsOn: [resource:payments-db, component:notification-service]
```

Because the file lives with the code, it's updated by the people who change the service,
in the same pull request. That's the whole reason it stays true when a wiki doesn't.

**Software templates.** "Create a new service" becomes a form: pick a name and an owner,
and Backstage scaffolds the repository from your template, wires the CI, registers it in
the catalogue, and hands back a link. New services start compliant instead of starting
from a copy of whatever was nearest.

**TechDocs.** Markdown that lives in each repository, rendered in the portal. Docs move
with the code, so they rot more slowly.

Plugins then pull in what a team actually wants on that page: CI status, Kubernetes
workloads, [Argo CD](/docs/devops/argocd/) sync state, on-call, cost, Sonar quality,
security findings.

## Try it

```bash
npx @backstage/create-app@latest
cd my-backstage-app && yarn dev
```

That gives you a running portal in minutes. Getting to a production one — auth, a real
database, your identity provider, the plugins you need, and an upgrade routine — is a
project, and pretending otherwise is how these efforts stall.

## Be honest about the cost

Backstage is **owned software**, not a SaaS you configure. Expect:

- Someone responsible for upgrading it. The project moves quickly and a portal frozen at
  last year's version becomes hard to extend.
- TypeScript/React work for anything beyond the stock plugins.
- A catalogue that is only as good as its adoption. A portal listing 40% of your services
  is worse than none, because people learn not to trust it.

Spotify's own hosted Portal and vendors like Cortex, OpsLevel, and Port exist precisely
because that cost is real. For a team under about fifty engineers, a well-maintained
README index and consistent repository templates often deliver most of the value for none
of the maintenance.

## The question worth answering first

Don't start with the portal. Start with the pain: is it *discovery* (nobody can find
things), *ownership* (nobody knows who to ask at 3am), or *bootstrapping* (every new
service is a snowflake)? Backstage does all three, but adopting it for a problem you don't
have yet is the most common way it becomes an abandoned internal project.

## Next

Shipping safely is half of it. Knowing what the system is doing afterwards →
[Observability & Reliability](/docs/observability/)
