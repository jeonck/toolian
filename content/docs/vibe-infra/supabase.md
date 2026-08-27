---
weight: 11070
title: "Supabase"
description: "Postgres with auth, file storage, realtime, and generated APIs attached — a backend you configure instead of write."
icon: "widgets"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

[Supabase](https://supabase.com/) starts from a plain Postgres database and adds the
things every app needs anyway: authentication, row-level authorisation, file storage,
realtime subscriptions, edge functions, and a REST and client API generated from your
schema. Open source, self-hostable, and the free tier is enough to launch on.

The pitch is that a small team can ship a real product without writing a backend. The
catch is worth knowing early, so it's below.

## Start

Create a project, then:

```bash
npm install @supabase/supabase-js
```

```js
import { createClient } from '@supabase/supabase-js';
const supabase = createClient(process.env.SUPABASE_URL, process.env.SUPABASE_ANON_KEY);

// auth
await supabase.auth.signInWithOAuth({ provider: 'github' });

// query — generated from your tables
const { data, error } = await supabase
  .from('posts')
  .select('id, title, author:profiles(name)')
  .eq('published', true)
  .order('created_at', { ascending: false })
  .limit(20);

// storage
await supabase.storage.from('avatars').upload(`${user.id}.png`, file);

// realtime
supabase.channel('posts')
  .on('postgres_changes', { event: 'INSERT', schema: 'public', table: 'posts' }, handle)
  .subscribe();
```

Four features, four lines each, and none of them needed a server you wrote.

## Row Level Security is not optional

The client talks to the database directly with an anon key that ships to the browser.
What stops a stranger reading every row is **Postgres row-level security**, and if you
forget to enable it, your table is public. This is the single most important thing on
this page.

```sql
alter table posts enable row level security;

create policy "read published posts"
  on posts for select
  using (published = true);

create policy "authors edit their own"
  on posts for update
  using (auth.uid() = author_id);
```

Test policies by querying as an anonymous user before you launch — the dashboard warns
about tables without RLS, and that warning is the one to act on.

## The free tier, and its one sharp edge

Two projects, a small database, file storage, generous auth users, and edge functions —
plenty for a launch. The sharp edge: **free projects pause after about a week of
inactivity** and need a manual unpause from the dashboard. A demo you show monthly will
be asleep when you open it. Paid plans don't pause.

Local development is a first-class path, and worth using:

```bash
npm install -g supabase
supabase init
supabase start          # full stack in Docker
supabase db diff -f add_posts    # generate a migration from your local changes
supabase db push
```

Migrations as files, applied the same way everywhere — the discipline the dashboard's
table editor quietly lets you skip.

## Where it stops fitting

- **Business logic in the client.** Fine at first, awkward later. Push rules into RLS,
  Postgres functions, and edge functions before the browser becomes your API layer.
- **Complex authorisation.** RLS is powerful and every policy is SQL you must reason
  about; deeply nested rules get hard to hold in your head.
- **You still own the database.** Indexes, connection limits, and slow queries are yours —
  the platform doesn't remove Postgres operations, it just delays when you meet them.

If you only want a database, [Neon](/docs/vibe-infra/neon/) is the lighter choice; if you
want auth alone with a different database, [Clerk](/docs/vibe-infra/clerk/) is on the next
page but one.

## Next

Reads that repeat every request belong in a cache → [Upstash](/docs/vibe-infra/upstash/)
