---
weight: 3060
title: "ast-grep"
description: "Search and rewrite code by its syntax tree, so a pattern matches structure instead of characters."
icon: "account_tree"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

[ripgrep](/docs/files/ripgrep/) searches text, and text is the wrong unit for code. A
regex for `console.log(...)` misses the call split across three lines, matches the one
inside a string, and can't tell you which argument is which. Rename a function with
`sed` and you rename it inside a comment too.

[ast-grep](https://ast-grep.github.io/) parses each file into a syntax tree and matches
against **that**. Your pattern is written as code, not as an escape-riddled regex:

```bash
ast-grep run -p 'console.log($MSG)' -l js
```

That matches the call however it's formatted, ignores the one in a string literal, and
binds the argument to `$MSG` so you can use it in a replacement.

## Install

```bash
brew install ast-grep
cargo install ast-grep --locked
npm install -g @ast-grep/cli
```

The command is `ast-grep`. A short alias `sg` exists, but on Linux `sg` is already a
system command for switching groups — use the full name in anything you commit.

## The pattern language

Three things to learn, and then you know it:

| Syntax | Matches |
|---|---|
| `$VAR` | One node — an expression, an identifier, an argument |
| `$$$ARGS` | Zero or more nodes — the rest of an argument list, a statement body |
| Literal code | Itself, structurally — whitespace and line breaks don't matter |

```bash
# every fetch call, whatever the arguments
ast-grep run -p 'fetch($$$)' -l ts

# a specific method on a specific object
ast-grep run -p 'router.get($PATH, $$$HANDLERS)' -l ts

# Python: bare except clauses
ast-grep run -p 'try:
    $$$BODY
except:
    $$$HANDLER' -l py
```

Build patterns in the [online playground](https://ast-grep.github.io/playground.html)
first. Seeing the parse tree next to your pattern turns "why doesn't this match" from
guesswork into reading.

## Rewriting, which is the point

Search is useful; the reason to install it is the rewrite.

```bash
# swap a logger, keeping the argument
ast-grep run -p 'console.log($MSG)' -r 'logger.info($MSG)' -l js --interactive

# migrate an assertion style across a test suite
ast-grep run -p 'expect($A).toBe($B)' -r 'expect($A).toStrictEqual($B)' -l ts -U
```

`--interactive` walks the matches and asks before each edit — start there. `-U` applies
everything at once, which is fine once you've seen the diff and have a clean git tree to
fall back on.

The migrations this makes routine are the ones people otherwise postpone for a quarter:
a renamed API across 400 call sites, an import path change, a deprecated option removed
from every config.

## Rules, for the checks you want to keep

A pattern you'll run more than once belongs in a YAML rule, and rules can be stricter
than a single pattern — matching only inside a certain context, or excluding a case.

```yaml
# rules/no-bare-except.yml
id: no-bare-except
language: python
severity: warning
message: Bare except swallows KeyboardInterrupt and SystemExit
rule:
  pattern: |
    try:
      $$$BODY
    except:
      $$$HANDLER
```

```bash
ast-grep new              # scaffold sgconfig.yml and the rules directory
ast-grep scan             # run every rule
ast-grep scan --json      # machine-readable, for CI
```

That's a project-specific linter in ten lines, for the conventions no off-the-shelf
linter knows about — "don't call this internal helper from outside the module", "don't
construct this client without a timeout".

## Where it fits

| Use | Tool |
|---|---|
| Find a string, a log message, a TODO | [ripgrep](/docs/files/ripgrep/) |
| Find or change a code construct | ast-grep |
| Rename a symbol with full type awareness | Your IDE, or an LSP-based refactor |

ast-grep understands syntax, not types or scope. It cannot tell two same-named methods on
different classes apart — for that you want the language's own tooling. Between "grep is
too blunt" and "spin up a compiler plugin", it fills the whole gap.

One habit worth keeping: **commit before a bulk rewrite**, and read the diff afterwards.
A pattern that matched more than you expected is easy to spot in a diff and awful to find
a week later.

## Next

With files under control, on to where you write the code →
[Editors & IDEs](/docs/editor/)
