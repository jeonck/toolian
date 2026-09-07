---
weight: 5047
title: "Open WebUI"
description: "The ChatGPT-shaped front end for models you host yourself — and for every API key you already have."
icon: "chat"
date: "2026-09-07"
lastmod: "2026-09-07"
draft: false
---

[Ollama](/docs/ai/ollama/) and [Slotstream](/docs/ai/slotstream/) give you a model and a
port. Neither gives you somewhere to talk to it, keep the conversation, upload a PDF, or
let your family use it. [Open WebUI](https://github.com/open-webui/open-webui) is that
missing half: a familiar chat interface in front of local models, hosted APIs, or both at
once.

## Run it

```bash
docker run -d -p 3000:8080 \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui --restart always \
  ghcr.io/open-webui/open-webui:main
```

Open `http://localhost:3000`. Variants save you wiring: `:ollama` bundles Ollama in the
same container, `:cuda` adds GPU acceleration (with `--gpus all`). If you'd rather not use
Docker:

```bash
pip install open-webui
open-webui serve
```

To point it at hosted models instead, pass the key — `-e OPENAI_API_KEY=…` — or add
providers in the settings. It speaks the OpenAI API shape, so anything that does works,
including Slotstream on `http://localhost:11434/v1`.

## What it adds over a raw model

- **Switch models mid-conversation**, local and hosted side by side, and compare answers
  from several at once.
- **Chat with your documents.** Upload files and reference them with `#`; retrieval is
  built in rather than an add-on.
- **Web search** as a toggle, so a local model stops being stuck in its training data.
- **Multiple users with roles** — the reason this becomes a household or team service
  rather than a personal one.
- **Pipelines and tools** for custom Python that runs between the message and the model.

## Worth knowing

- **The licence is not plain BSD.** It's BSD-3 with a branding clause: you may not remove
  or replace the Open WebUI name and logo unless your deployment has **50 or fewer users
  in a rolling 30 days**, you've been given written permission, or you hold an enterprise
  licence. Self-hosting for yourself, a team, or a family is unaffected. White-labelling
  it inside a product is not.
- **The UI is not the bottleneck; the model is.** A friendlier front end doesn't make a
  7B model answer like a frontier one — see the honest expectations on the
  [Ollama page](/docs/ai/ollama/).
- **If you expose it beyond localhost, put auth in front of it** and keep it off the open
  internet unless you mean it. It holds your conversations and your API keys.

## Next

Local models are one source of tokens; stacked free tiers are another →
[FreeLLMAPI](/docs/ai/freellmapi/)
