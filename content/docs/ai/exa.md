---
weight: 5110
title: "Exa"
description: "Web search built for models rather than people — meaning-based retrieval, clean extracted text, and a research endpoint with citations."
icon: "travel_explore"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Point an agent at a conventional search API and you get what a person would get: ten blue
links, ranked for clicks, pointing at pages full of navigation, cookie banners, and
advertising. The agent then fetches each one, strips the HTML badly, and spends most of
its context on furniture.

[Exa](https://exa.ai/) is built for the other reader. Its index is searched by **meaning**
rather than keywords, and every result can come back with the page's **clean extracted
text**, so the agent gets content instead of markup.

## Search and read in one call

```bash
pip install exa-py
```

```python
from exa_py import Exa
exa = Exa(api_key="...")

results = exa.search_and_contents(
    "engineering blog posts about migrating off Kubernetes",
    num_results=5,
    text=True,
    start_published_date="2026-01-01",
)

for r in results.results:
    print(r.title, r.url)
    print(r.text[:500])
```

Describing what you want in a sentence works better here than keyword stuffing, because
the query is embedded rather than matched. The filters matter as much as the query:
publication date, domain include and exclude lists, and result type all narrow the search
before ranking, which is how you keep an agent away from SEO farms.

Two endpoints beyond plain search are worth knowing:

- **`answer`** — a direct answer with the sources it used. Good when you want one fact and
  a citation rather than a reading list.
- **`research`** — a multi-step agentic search that plans, gathers, and returns structured
  output with web-grounded citations. Slower and more expensive per call; appropriate when
  the question genuinely needs several searches.

There's an official **MCP server**, so an MCP-capable agent can use all of this without
you writing a client at all.

## Costs, and how not to be surprised

Pricing is per thousand requests and differs sharply by endpoint — plain search is cheap,
the research endpoint costs several times more, and extra results and page summaries bill
on top. New accounts get free credits with a monthly top-up, which is enough to build and
evaluate against.

Three habits keep the bill sane, and they also make the agent better:

- **Cache aggressively.** The same query from ten users should hit the API once —
  [Upstash](/docs/vibe-infra/upstash/) with a day's expiry is the usual answer.
- **Default to `search`.** Reach for `research` only when a single search genuinely can't
  answer the question. It's easy to wire the expensive endpoint in as the default and
  discover the cost later.
- **Ask for fewer results and less text.** More context is not more accuracy; five clean
  results usually beat twenty noisy ones.

## Where it fits among the alternatives

| Need | Reach for |
|---|---|
| Meaning-based results plus clean text, for an agent | Exa |
| Cheap, familiar, keyword-shaped web search | Brave Search API, SerpAPI, Tavily |
| Search over **your own** documents | An index of your own — [Meilisearch](/docs/vibe-infra/meilisearch/) or pgvector |
| One-off page fetch in a script | [curl](/docs/network/curl/) and a parser |

The distinction that matters: Exa searches the public web, so it answers "what's out there
about this". It has nothing to say about your internal documents — for those you build
your own index, often from documents parsed with [Docling](/docs/files/docling/) or
[MinerU](/docs/files/mineru/).

And treat every retrieved page as untrusted input. Content an agent fetches from the web
can contain instructions aimed at the agent; that it came back from a search does not make
it safe to follow.

## Next

Changes made with AI need version control around them →
[Git & Collaboration](/docs/git/)
