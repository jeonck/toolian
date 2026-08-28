---
weight: 5100
title: "Composio"
description: "Giving an agent authenticated access to real applications — Gmail, Slack, GitHub, Jira — without building an OAuth flow per service."
icon: "extension"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Wiring an agent to one external service is a morning's work. Wiring it to Gmail, Slack,
GitHub, Notion, and Jira is a quarter — because the hard part was never the API call. It's
OAuth per provider, token storage and refresh, per-user credentials, scope management, and
rate limits, five times over, before the agent does anything useful.

[Composio](https://composio.dev/) does that part. It exposes a catalogue of applications
as tools an agent can call, and **owns the authentication**: users connect their accounts
at runtime, the platform holds and refreshes the tokens, and your code never sees a
credential.

## Use it

```bash
pip install composio          # a TypeScript SDK exists too
```

```python
from composio import Composio

composio = Composio()

# a user connects their own account; the OAuth dance is the platform's problem
request = composio.toolkits.authorize(user_id="user-42", toolkit="GITHUB")
print(request.redirect_url)

# hand the tools to whatever agent framework you're using
tools = composio.tools.get(user_id="user-42", toolkits=["GITHUB", "SLACK"])
```

The tools arrive in the shape your framework expects — OpenAI tool definitions, LangChain
tools, CrewAI, the Anthropic SDK — so it slots into an existing agent rather than asking
you to adopt a new one.

There's also an **MCP endpoint**, which is the shortest path of all: one server address
gives an MCP-capable client access to the catalogue, instead of installing and configuring
one MCP server per application.

## What it's actually solving

| Problem | Without | With |
|---|---|---|
| OAuth per provider | You build and maintain each flow | Handled, including refresh |
| Per-user credentials | Your database stores tokens | Held by the platform, scoped per user |
| Tool definitions | Hand-written per API | Generated, in your framework's format |
| Multi-app workflows | Glue code between clients | One interface across the catalogue |

Multi-tenancy is the case where this pays for itself. "Every user connects **their own**
Slack" is a genuinely hard thing to build safely and a configuration line here.

## Before you build on it

- **You are handing a third party your users' OAuth tokens.** That's the trade, and it's a
  real one: read the security and data-handling terms, and check whether your compliance
  position allows it. The project is MIT-licensed with a self-hostable option if not.
- **Free tiers move.** Usage-based pricing with a monthly free allowance of tool calls is
  the current shape; check the numbers against your expected volume rather than inheriting
  someone's blog post.
- **Give agents the smallest toolkit that works.** An agent holding twenty applications
  makes worse decisions than one holding three, and the blast radius of a mistake is
  larger. Scope by task.
- **Confirm before consequential actions.** A tool that sends email or closes tickets
  should be behind a human confirmation until you trust it — the platform grants access,
  it doesn't supply judgement.

## Where it fits

If your agent only needs your own APIs, you don't need this: define the tools yourself and
keep the surface small. Composio earns its place when the agent must reach **other
people's** software on **your users'** behalf, and the authentication — not the logic — is
what's standing between you and shipping.

## Next

An agent that can act still needs to know things → [Exa](/docs/ai/exa/)
