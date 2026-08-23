---
weight: 11010
title: "Railway"
description: "Deploying a backend and a frontend from one repository, with the database attached, in about five minutes."
icon: "train"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

Most hosts want your frontend somewhere and your API somewhere else. Railway takes a
repository, works out how to build it, and runs it — API, worker, frontend, and a
Postgres instance side by side in one project, wired together by environment variables
it fills in for you.

That makes it the default when your app has a **server** in it: a Django or FastAPI
backend, an Express API, a Discord bot, a cron worker, anything that has to stay running
and hold a connection to a database.

## What the free tier really is

Railway's free allowance is the smallest in this category and the one people are most
often surprised by.

| Stage | What you get |
|---|---|
| Trial | A one-time $5 credit, valid 30 days, no card required |
| Free plan | $1 of credit per month once the trial ends |
| Hobby | $5/month minimum, and usage bills on top of it |

$1/month runs an experiment, not a service that stays awake. Treat Railway as *free to
prove the idea, then $5/month to keep it alive* — and be glad the ceiling appears as a
paused service rather than a surprise invoice.

## Deploy from the dashboard

The path with the fewest steps:

1. **New Project → Deploy from GitHub repo**, pick the repository.
2. Railway detects the language and builds it. Node, Python, Go, Ruby, Rust, and a
   `Dockerfile` all work without configuration.
3. **New → Database → Add PostgreSQL** in the same project.
4. In your service's **Variables** tab, reference the database:
   `DATABASE_URL=${{Postgres.DATABASE_URL}}`.
5. **Settings → Networking → Generate Domain** gives you a public
   `*.up.railway.app` URL.

The `${{...}}` reference matters: it re-resolves if the database is recreated, so
nothing breaks the way a pasted connection string does.

## Deploy from the CLI

```bash
npm install -g @railway/cli
railway login
railway init                 # create or link a project
railway up                   # build and deploy the current directory
railway logs                 # tail the running service
railway variables            # list what the service can see
railway run npm run dev      # run locally with production variables injected
```

`railway run` is the one to remember — it saves keeping a second copy of every secret in
`.env`.

## Running a frontend and a backend together

Add two services from the same repository and give each a root directory
(**Settings → Root Directory**, e.g. `apps/api` and `apps/web`). Then point the frontend
at the backend by reference rather than by URL:

```
NEXT_PUBLIC_API_URL=https://${{api.RAILWAY_PUBLIC_DOMAIN}}
```

Preview environments work the same way, which is what makes a monorepo pleasant here:
one push, both halves rebuilt, references still correct.

## Keeping the bill down

- **Sleep what doesn't need to be awake.** App sleeping suspends a service with no
  traffic; the first request after that pays a cold start of a few seconds.
- **Watch egress.** Serving images and video from the app container is the usual reason
  a $5 project becomes a $20 one — put files on [R2](/docs/vibe-infra/cloudflare-r2/)
  instead.
- **Set a usage limit** in the project settings. Railway will stop the service instead
  of letting it run up a bill.
- **One project, many services.** Splitting into several projects loses the private
  network between them and pushes traffic out over the public internet.

## When it isn't the right pick

A pure static site or a Next.js app with no long-running process gets a bigger free tier
and a better CDN from [Vercel](/docs/vibe-infra/vercel/). Railway earns its keep the
moment something has to *stay running*.

## Next

If there's no server in your app → [Vercel](/docs/vibe-infra/vercel/)
