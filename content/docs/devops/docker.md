---
weight: 8010
title: "Docker"
description: "Container fundamentals for pinning an environment as code — images, containers, Dockerfiles."
icon: "inventory"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Docker's value is less about virtualisation and more about **writing an environment
down as code**. Half a day of local setup for a new teammate becomes one line:
`docker compose up`.

## Install

```bash
brew install --cask docker          # Docker Desktop
brew install --cask orbstack        # a lighter alternative on macOS
```

On Linux, follow your distribution's official `docker-ce` instructions.

## Three concepts

| Concept | Meaning |
|---|---|
| **Image** | A read-only snapshot of an environment |
| **Container** | A running instance of an image |
| **Volume** | Storage that survives the container |

## Basic commands

```bash
docker run -d --name pg -p 5432:5432 \
  -e POSTGRES_PASSWORD=secret postgres:16

docker ps                    # running containers
docker ps -a                 # including stopped ones
docker logs -f pg            # follow the logs
docker exec -it pg psql -U postgres    # run a command inside
docker stop pg && docker rm pg
```

| Flag | Meaning |
|---|---|
| `-d` | Run in the background |
| `-p host:container` | Publish a port |
| `-e KEY=VALUE` | Environment variable |
| `-v hostpath:containerpath` | Mount a volume |
| `--rm` | Delete on exit |
| `-it` | Interactive terminal |

## Writing a Dockerfile

```dockerfile
# stage 1: build
FROM node:22-slim AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# stage 2: run, without the build tooling
FROM node:22-slim
WORKDIR /app
ENV NODE_ENV=production
COPY package*.json ./
RUN npm ci --omit=dev
COPY --from=builder /app/dist ./dist
USER node
EXPOSE 3000
CMD ["node", "dist/main.js"]
```

Two things carry most of the benefit:

- **Layer caching.** Copy what changes rarely (dependencies) first and what changes
  often (source) last. Reordering alone can make builds several times faster.
- **Multi-stage builds.** Build tools never reach the final image, which shrinks both
  the size and the attack surface.

Don't forget `.dockerignore`:

```
node_modules
.git
dist
*.log
.env
```

## Build and run

```bash
docker build -t myapp:dev .
docker run --rm -p 3000:3000 --env-file .env myapp:dev
```

## Cleaning up

Containers and images quietly eat disk.

```bash
docker system df           # check usage
docker container prune     # remove stopped containers
docker image prune -a      # remove unused images
docker system prune -a --volumes    # everything, volumes included (careful)
```

## Frequent problems

| Symptom | Cause and fix |
|---|---|
| Port conflict | Something already holds it. Check with `lsof -i :5432` |
| Edits don't show up | The source was copied into the image. Mount it in dev: `-v $(pwd):/app` |
| Fails to run on Apple Silicon | Specify `--platform linux/amd64` |
| Container exits immediately | No foreground process. Check `docker logs` |

## Next

To bring several services up at once → [Docker Compose](/docs/devops/docker-compose/)
