---
weight: 5035
title: "Kiro"
description: "AWS's agentic IDE and CLI, built around writing the spec before the code."
icon: "checklist"
date: "2026-09-04"
lastmod: "2026-09-04"
draft: false
---

Most agents start typing from a one-line prompt. Kiro's bet is that the prompt is the
problem: it makes you settle **requirements, then design, then a task list** first, and
only then writes code against them. The same agent shows up as a desktop IDE, a
terminal CLI, a web UI, and a mobile app, all reading the same `.kiro` directory.

## Install

Download the IDE from [kiro.dev](https://kiro.dev/), or install the terminal agent:

```bash
brew install --cask kiro-cli
kiro-cli login          # AWS Builder ID, Identity Center, Google, or GitHub
kiro-cli chat
```

The IDE imports your VS Code settings and extensions, so the editor half feels familiar
from the first launch.

## Spec-driven development

Ask for a feature and Kiro doesn't edit files yet — it writes three documents you
approve one at a time.

| File | What's in it |
|---|---|
| `requirements.md` | User stories with acceptance criteria, in structured notation |
| `design.md` | Architecture, sequence diagrams, data flow, error handling, test strategy |
| `tasks.md` | Discrete implementation tasks, tracked as they complete |

Tasks run individually or in parallel waves where nothing depends on anything else.
The value isn't the ceremony — it's that a disagreement about scope surfaces in
`requirements.md`, where fixing it costs a sentence, instead of after 600 lines of
generated code.

For a bug, `bugfix.md` takes the place of requirements: current behaviour versus
expected.

## Steering files

Project rules live as Markdown and get pulled into the agent's context automatically —
the same job `CLAUDE.md` does for Claude Code, with control over *when* each file
loads.

```
.kiro/steering/
  product.md      # what this product is
  tech.md         # stack, versions, commands
  structure.md    # where things go
~/.kiro/steering/ # your own rules, every project
```

```markdown
---
inclusion: fileMatch
fileMatchPattern: "components/**/*.tsx"
---

- Components are function components. No class components.
- Styling is Tailwind only; no CSS modules.
```

`inclusion` takes `always` (the default), `fileMatch`, `manual` (you call it with
`#file-name` in chat), or `auto`, where Kiro decides from the description. The CLI
ignores the modes and loads everything.

## Hooks and MCP

Hooks fire an agent action on an event — a file saved, a task finished — so "update the
tests when this file changes" stops being something you remember to ask for. MCP
servers connect external tools and APIs, and are managed from the CLI:

```bash
kiro-cli mcp add --name github --command "npx -y @modelcontextprotocol/server-github"
kiro-cli mcp list
```

## Useful CLI flags

```bash
kiro-cli chat --resume                    # pick up the last conversation
kiro-cli chat --no-interactive "..."      # one answer to STDOUT — for CI
kiro-cli chat --effort high               # low | medium | high | xhigh | max
kiro-cli agent create reviewer            # a named agent with its own config
kiro-cli doctor                           # when something's wrong with the install
```

`--trust-all-tools` skips every confirmation. Convenient in a sandbox, a bad idea
anywhere your credentials can reach production.

## Where it fits

| Reach for Kiro when | Reach for something else when |
|---|---|
| The feature is big enough that scope drifts | It's a two-file change you can describe exactly |
| More than one person has to agree on behaviour | You're exploring and don't know the shape yet |
| You want the plan reviewed in a PR, not a chat log | The ceremony would outweigh the work |

The spec files are ordinary Markdown in your repo, which means they review like code
and survive after the agent's session is gone.

## Next

When code can't leave your machine → [Ollama](/docs/ai/ollama/)
