---
weight: 7060
title: "AutoCLI"
description: "Pulling structured data out of Reddit, Hacker News, and dozens of other sites with one command instead of a scraper."
icon: "download_for_offline"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

You want the top Hacker News stories in JSON. The honest options used to be: read the
site's API docs and write a client, or write a scraper and maintain it forever.
[AutoCLI](https://github.com/nashsu/AutoCLI) collapses that into one command per site —
Reddit, Hacker News, YouTube, Twitter/X and a long tail of others — with the output
already shaped as JSON, YAML, CSV, Markdown, or a table.

It's Rust, Apache-2.0, and the appeal is narrow but real: the ten-minute question that
doesn't justify a scraper.

## Install

```bash
curl -fsSL https://raw.githubusercontent.com/nashsu/autocli/main/scripts/install.sh | sh
```

Binaries for macOS, Linux, and Windows are on the releases page, and
`cargo build --release` works from a clone. As with any install script, read it before
you pipe it into a shell — or take the binary and skip the question.

## Use it

```bash
autocli hackernews top --limit 10
autocli hackernews top --limit 10 --format json
autocli reddit hot --limit 5 --format yaml
autocli twitter search "rust lang" --limit 10 --format csv
```

The shape is always `autocli <site> <command> [options]`, and `autocli <site> --help`
lists what a given site supports. Default output is a table for reading; `--format json`
is what you pipe.

```bash
autocli hackernews top --limit 30 --format json | jq -r '.[] | select(.score > 200) | .title'
```

That pairs it with [jq](/docs/files/jq/) the way the rest of this site expects: one tool
fetches, one tool filters, neither knows about the other.

## Where it earns its place

- **A morning digest.** A cron job that pulls three feeds, filters by score, and mails
  you the result — a twenty-line shell script instead of a project.
- **Research you'd otherwise do by scrolling.** Pull a week of posts to a CSV and read
  them in a spreadsheet.
- **Seeding a demo.** Real-looking data for a prototype without writing fixtures.

## Read this before you build on it

- **Site coverage is inherently fragile.** Anything that reads sites rather than
  contracts breaks when a page changes. Fine for a script you run by hand; risky as a
  dependency of something that must not fail. For Hacker News and Reddit specifically,
  both publish official APIs — for a production job, use those.
- **Terms of service and rate limits are yours to respect.** The tool will happily make
  more requests than a site wants. Pace it, cache what you fetch, and don't redistribute
  content you don't have the right to.
- **The browser mode is the part to think hardest about.** Commands that reach
  logged-in content work through a Chrome extension using your own session. That's your
  real account acting automatically — convenient, and worth a moment's thought about
  which account, and which site's rules on automation.

Treat it as a fast way to answer a question, not as infrastructure.

## Next

On to shipping → [Containers & Deploys](/docs/devops/)
