---
weight: 11130
title: "Meilisearch"
description: "Typo-tolerant, instant search in one binary — the middle ground between SQL LIKE and running Elasticsearch."
icon: "manage_search"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Search starts as `WHERE title LIKE '%term%'`. It works until someone misspells a word,
searches two words in the wrong order, or expects results as they type — and then the
options look like a week with Elasticsearch or a bill from Algolia.

[Meilisearch](https://www.meilisearch.com/) sits between them: a single Rust binary,
typo-tolerant and fast by default, with an API you can learn in an afternoon. Open source,
self-hostable, with a paid cloud if you'd rather not run it.

## Run and index

```bash
docker run -p 7700:7700 -e MEILI_MASTER_KEY=aSampleMasterKey \
  getmeili/meilisearch:latest
```

```bash
npm install meilisearch
```

```js
import { MeiliSearch } from 'meilisearch';
const client = new MeiliSearch({ host: 'http://localhost:7700', apiKey: process.env.MEILI_KEY });

const index = client.index('posts');

await index.addDocuments([
  { id: 1, title: 'Deploying with Railway', tags: ['deploy'], published_at: 1735689600 },
  { id: 2, title: 'Postgres branching with Neon', tags: ['database'], published_at: 1738368000 },
]);

const results = await index.search('postgrs branchng');   // typos are fine
```

That last line is the demo that sells it: misspellings still match, and the response
comes back in a handful of milliseconds.

## Configure the three things that matter

```js
await index.updateSettings({
  searchableAttributes: ['title', 'summary', 'body'],   // order = importance
  filterableAttributes: ['tags', 'author_id', 'published'],
  sortableAttributes: ['published_at'],
});
```

- **searchableAttributes** in priority order — a match in the title should outrank one
  buried in the body.
- **filterableAttributes** for anything you'll narrow by. Filters are exact; search is
  fuzzy.
- **sortableAttributes** for anything you'll order by.

```js
await index.search('postgres', {
  filter: 'tags = database AND published = true',
  sort: ['published_at:desc'],
  limit: 20,
  attributesToHighlight: ['title'],
});
```

## Keys, and the one that must not leak

Generate a **search-only key** for the browser and keep the master key on the server. It's
the same mistake as a database password in client code, and it's easy to make because the
quickstart hands you the master key first.

```js
const searchKey = await client.createKey({
  actions: ['search'],
  indexes: ['posts'],
  expiresAt: null,
});
```

## Keeping the index in step with the database

Postgres stays the source of truth; the index is derived and disposable. Two patterns:

- **Reindex on write.** After a create or update, push the document — ideally from a
  background job like [Inngest](/docs/vibe-infra/inngest/) so a slow index doesn't slow
  the request.
- **Rebuild periodically.** A nightly full reindex into a new index, then swap it, keeps
  drift from accumulating. Cheap, and it makes the "did the index miss something?" worry
  go away.

## Choosing among the options

| | Fits |
|---|---|
| **Postgres full-text search** | Modest data, no extra service — try `tsvector` first |
| **Meilisearch** | Instant, typo-tolerant search you can self-host cheaply |
| **Typesense** | Very similar; pick on which API and hosting you prefer |
| **Algolia** | Don't want to run anything, and the bill is acceptable |
| **Elasticsearch / OpenSearch** | Log analytics and complex aggregations, not just search |

Genuinely start with Postgres. A `tsvector` column and a GIN index covers more products
than people expect, and one fewer service to run is worth a lot. Move here when
typo-tolerance, ranking, or search-as-you-type becomes a real requirement.

## Next

Search tells you what people looked for; analytics tells you what they did →
[PostHog](/docs/vibe-infra/posthog/)
