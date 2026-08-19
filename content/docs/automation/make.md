---
weight: 9030
title: "Make and Makefiles"
description: "Gathering every project command into one file so everything becomes `make test`."
icon: "play_arrow"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

If you copy long commands out of a README every time, a single Makefile tidies that up.
Make exists on every Unix-like system and doesn't care what language you write in.

## A minimal Makefile

`Makefile` at the project root (tab indentation is mandatory):

```makefile
.PHONY: help install dev test lint build clean

help:  ## Show the available commands
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | \
	  awk 'BEGIN {FS = ":.*?## "}; {printf "  \033[36m%-12s\033[0m %s\n", $$1, $$2}'

install:  ## Install dependencies
	npm ci

dev:  ## Run the development server
	docker compose up -d
	npm run dev

test:  ## Run the tests
	npm test

lint:  ## Lint and check formatting
	npm run lint
	npm run format:check

build:  ## Production build
	npm run build

clean:  ## Remove build output
	rm -rf dist node_modules/.cache
	docker compose down
```

`make` or `make help` prints the commands with their descriptions, so a new teammate can
see what's possible without reading the README.

## Syntax to know

| Syntax | Meaning |
|---|---|
| `.PHONY: name` | Always run, even if a file of that name exists |
| `target: deps` | Run the dependency targets first |
| `@command` | Don't echo the command itself |
| `-command` | Continue even if it fails |
| `$$` | A literal `$` for the shell (distinct from Make's `$`) |
| `VAR ?= value` | Keep an existing definition |

**Indentation must be tabs.** Spaces produce
`Makefile:5: *** missing separator`. Configure your editor to preserve tabs in
Makefiles (`[Makefile] indent_style = tab` in `.editorconfig`).

## Variables and arguments

```makefile
ENV ?= dev
IMAGE = myapp:$(ENV)

build:
	docker build -t $(IMAGE) .

deploy:
	./deploy.sh $(ENV)
```

```bash
make deploy ENV=prod
```

## Each line is its own shell

```makefile
# wrong — the cd does not carry to the next line
wrong:
	cd src
	npm test

# right
right:
	cd src && npm test
```

## Alternatives

| Tool | Character |
|---|---|
| **just** | None of Make's traps (tabs, separate shells). Uses a `justfile` |
| **npm scripts** | Already there in a Node project; strains as things grow |
| **Taskfile** | YAML based, with dependencies and conditions |

```bash
brew install just
just --list
```

Make's biggest advantage is that **it is already installed everywhere**. Pick Make when
not requiring a new install matters, and `just` when syntax comfort wins.

## Next

For work that has to run at a set time →
[cron and launchd](/docs/automation/schedulers/)
