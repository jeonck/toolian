---
weight: 8020
title: "Docker Compose"
description: "Defining your app, database, and cache in one file and starting them with one command."
icon: "layers"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Real projects don't stop at one container — the app, a database, a cache, and a queue
all need to be up. Compose describes that in a single YAML file and starts it with
`docker compose up`.

## compose.yaml

```yaml
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgres://postgres:secret@db:5432/app
      REDIS_URL: redis://cache:6379
    volumes:
      - .:/app
      - /app/node_modules      # protect it from being shadowed by the host
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: app
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      retries: 5

  cache:
    image: redis:7-alpine

volumes:
  pgdata:
```

Containers **find each other by service name**. That's why the app above connects to
`db` rather than `localhost`.

## Commands

| Command | Action |
|---|---|
| `docker compose up -d` | Start everything in the background |
| `docker compose up --build` | Rebuild images, then start |
| `docker compose ps` | Status |
| `docker compose logs -f app` | Logs for one service |
| `docker compose exec app sh` | Shell into a running container |
| `docker compose run --rm app npm test` | One-off command |
| `docker compose down` | Stop and clean up |
| `docker compose down -v` | Also delete volumes (wipes data) |

## Splitting environments

Use a base file plus an override.

```bash
# compose.yaml (shared) + compose.override.yaml (local, applied automatically)
docker compose up

# with a production configuration
docker compose -f compose.yaml -f compose.prod.yaml up -d
```

`compose.override.yaml` merges automatically by filename, so local-only volume mounts
and debug ports belong there.

## The `.env` file

```bash
# .env
POSTGRES_PASSWORD=secret
APP_PORT=3000
```

```yaml
services:
  app:
    ports:
      - "${APP_PORT}:3000"
```

Always gitignore `.env` and commit a `.env.example` instead so the required keys are
discoverable.

## Frequent problems

| Symptom | Fix |
|---|---|
| App can't reach the database | Use the service name, not `localhost` |
| The database isn't ready yet | Pair `depends_on` with a `healthcheck` |
| Code changes don't apply | Check the source volume mount and hot reload settings |
| `node_modules` disappears | Protect it with an anonymous volume (`/app/node_modules`) |
| Port already in use | Change the port value in `.env` |

## Next

For the same containers without a root daemon → [Podman](/docs/devops/podman/)
