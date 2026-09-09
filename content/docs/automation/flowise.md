---
weight: 9060
title: "Flowise"
description: "Building an AI agent or RAG pipeline by dragging nodes onto a canvas instead of writing the glue code."
icon: "account_tree"
date: "2026-09-07"
lastmod: "2026-09-09"
draft: false
---

[n8n](/docs/automation/n8n/) connects services to each other on a canvas. [Flowise
](https://github.com/FlowiseAI/Flowise) does the same thing one layer up: the nodes are
**LLMs, prompts, retrievers, vector stores, tools, and memory**, and what you build is an
agent or a RAG pipeline rather than an integration.

It's the answer to "I want a chatbot over our documentation, but not to write LangChain
from scratch."

## Start it

```bash
npm install -g flowise
npx flowise start
```

Then `http://localhost:3000`. There's a `docker compose up -d` path in the repository's
`docker/` folder if you'd rather it live in a container.

## What you actually build

| Flow | The nodes in it |
|---|---|
| Docs chatbot | Document loader → text splitter → embeddings → vector store → retrieval QA chain |
| Support agent | Chat model + tools (search, HTTP request, calculator) + conversation memory |
| Data extraction | File input → LLM with a structured-output parser → webhook |
| Multi-step agent | Several agents in sequence, each with its own tools and system prompt |

Every finished flow gets an API endpoint and an embeddable chat widget, which is the part
that makes it more than a toy: the canvas is the authoring surface, but the result is
something your app can call.

## Where it helps and where it doesn't

**It helps** when you're exploring — swapping a vector store, trying a different chunk
size, seeing which prompt actually works. Seeing the pipeline as a diagram makes the
"where did this answer come from" question answerable, and non-programmers on the team can
follow it.

**It gets in the way** once the thing is real. A canvas diffs badly in code review,
version control is exports rather than pull requests, and the moment you need behaviour a
node doesn't offer, you're writing custom code anyway — at which point the same pipeline
in fifty lines of Python is easier to test and deploy. Prototype here; port what survives.

## Two practicalities

- **Licence is mostly Apache 2.0, with a commercial carve-out.** The
  `packages/server/src/enterprise` directory and some individually marked files are under
  a commercial licence. Self-hosting for your own work is fine; read it before you build
  a product on top.
- **Keys live in the flow.** A Flowise instance holds credentials for every model and
  service its nodes touch. Don't leave it on an open port, and give it its own keys with
  their own limits.

## Next

For a narrower automation that publishes on a schedule →
[Ddalkkak Threads Factory](/docs/automation/ddalkkak-threads/)
