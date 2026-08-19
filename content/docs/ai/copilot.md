---
weight: 5020
title: "GitHub Copilot"
description: "Inline completion inside your editor. Biggest payoff on repetitive code."
icon: "auto_fix_high"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Copilot greys in the code it thinks follows your cursor. It doesn't roam a project the
way an agent does, but by **sheer frequency of small time savings** it's the form of
assistance that helps most often.

## Install

Install the `GitHub Copilot` extension from the VS Code marketplace and sign in with
your GitHub account. Plugins exist for JetBrains, Neovim, and Visual Studio too.

```bash
# Neovim
git clone https://github.com/github/copilot.vim \
  ~/.config/nvim/pack/github/start/copilot.vim
# then run :Copilot setup
```

## Basic controls

| Action | Key |
|---|---|
| Accept the suggestion | `Tab` |
| Dismiss it | `Esc` |
| Next / previous suggestion | `⌥]` / `⌥[` (Alt) |
| Open several in a panel | `⌃⏎` (Ctrl+Enter) |
| Inline chat | `⌘I` / `Ctrl+I` |

## Getting better suggestions

Copilot predicts from **surrounding context**, so the clearer that is, the more accurate
it gets.

1. **Write the intent as a comment first.**
   ```python
   # Read the CSV, parse the date column, drop rows with missing values
   def load_sales(path: str) -> pd.DataFrame:
   ```
2. **Write the signature and types first.** A declared return type sharply improves the
   body it produces.
3. **Keep related files open.** Open tabs are used as context.
4. **Name things precisely.** `parseInvoiceRow` draws better suggestions than
   `handleData`.

## Where it shines

- Writing test cases (write one and it continues the pattern)
- Repetitive mapping and transformation code
- Regexes and date format strings — the things nobody memorises
- Doc comments (JSDoc, docstrings)

## Cautions

- **Plausible wrong answers happen**, especially invented API signatures. For an
  unfamiliar library, check the docs.
- **Don't ship security code unreviewed.** Authentication, encryption, and permission
  checks always get a human read.
- **Check your organisation's policy.** Suggestion filtering or usage limits may apply
  to private code.

## Alongside Claude Code

The two barely overlap.

| Situation | Tool |
|---|---|
| Finish this line quickly | Copilot |
| Change several files | Claude Code |
| Understand the code | Either (ask in chat) |

## Next

For an editor built around AI from the start → [Cursor](/docs/ai/cursor/)
