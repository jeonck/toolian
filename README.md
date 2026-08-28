# Toolian

A knowledge base introducing the tools that raise the productivity and comfort of
everyday IT work, with a short, practical guide to each one.

**https://toolian.metacog.co.kr**

## Built with

- Hugo (extended) + [Lotus Docs](https://github.com/colinwilson/lotusdocs) (a Hugo Module)
- Built by GitHub Actions and deployed to GitHub Pages
- 14 categories, 108 pages

| Category | Contents |
|---|---|
| Getting Started | Choosing tools, package managers, dotfiles |
| Terminal & Shell | Terminal apps, zsh/Starship, tmux, fzf, zoxide, hyperfine |
| Files & Search | ripgrep, fd, bat, eza, jq, ast-grep |
| Editors & IDEs | VS Code, Neovim, JetBrains, portable editing skills |
| AI Coding Tools | Claude Code, Copilot, Cursor, Ollama, Graft, Codebase Memory MCP, Agency Agents, OpenMontage, HyperFrames |
| Git & Collaboration | Git workflow, gh, Gitea, Forgejo, GitLab, lazygit, delta, hunk, pre-commit |
| Network & APIs | curl, HTTPie, Bruno/Postman, tunnelling, AutoCLI |
| Containers & Deploys | Docker, Compose, Podman, local k8s, kubectl/k9s, Helm, Kustomize, cert-manager, Istio, policy, Actions, Jenkins, GitLab CI, SonarQube, Nexus, Argo CD, Flux, CodePipeline, Terraform, Ansible, secrets, Trivy, Falco, Backstage |
| Observability & Reliability | Prometheus, Grafana, Loki, OpenTelemetry, k6, chaos engineering, OpenCost |
| Automation & Shortcuts | Raycast, PowerToys, Make, cron/launchd, n8n |
| Linux Desktop | Zen Browser, Webapp Manager, Zathura |
| Docs & Notes | Obsidian, Joplin, Markdown, Mermaid, Excalidraw |
| Vibe Coding Infra | Railway, Fly.io, Vercel, Cloudflare Workers, Postgres, Neon, Supabase, Upstash, R2, Clerk, Resend, Inngest, Meilisearch, PostHog, Sentry, Lemon Squeezy, Groble |
| AI Media Tools | Raphael, Krea, Magnific, Clipdrop, ElevenLabs |

## Running locally

```bash
hugo server
```

You need Hugo Extended and a Go toolchain — Lotus Docs ships as a Hugo Module, so module
resolution requires Go.

## Adding a page

Create `content/docs/<category>/<page>.md`. The `weight` in the front matter **must be
unique across the whole site** and follows this formula:

```
weight = 1000 × (category position) + 10 × (position within the category)
```

Lotus Docs computes previous/next navigation by sorting every page under `/docs/` by
weight, so repeating 10/20/30 in each category makes those cards jump between unrelated
categories.

## Deployment

Pushing to `main` triggers `.github/workflows/hugo.yml`, which builds the site and
deploys it to Pages. The custom domain lives in `static/CNAME`; Hugo copies it into
`public/CNAME` on every build.

## Local overrides

`layouts/docs/single.html` is a copy of the theme's file with one addition:
`gantt.useWidth` in the mermaid config. Without it, gantt charts render into a zero-width
SVG.
