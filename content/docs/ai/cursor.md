---
weight: 5030
title: "Cursor"
description: "A VS Code fork that puts AI editing front and centre — and imports your existing setup."
icon: "bolt"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Cursor forks VS Code and promotes AI features into the core workflow. Because it keeps
most VS Code extensions, keymaps, and settings, the cost of switching is low.

## Install

```bash
brew install --cask cursor
```

On first launch it offers to import your VS Code setup — extensions, theme, and key
bindings come across.

## Three features that matter

| Feature | Key | Purpose |
|---|---|---|
| **Inline edit** | `⌘K` / `Ctrl+K` | Change the selection from a plain-language instruction |
| **Chat** | `⌘L` / `Ctrl+L` | Ask questions about the code |
| **Agent mode** | Toggle in the chat panel | Make changes across several files |

### Inline edit is the one you'll use most

Select the code, press `⌘K`, and say "turn this loop into a map and add a null check."
The change appears as a diff for you to accept or reject.

## Specifying context

In chat, `@` selects what it should look at.

| Input | Meaning |
|---|---|
| `@filename` | A specific file |
| `@folder` | A whole folder |
| `@Codebase` | Search the repository |
| `@Docs` | Registered library documentation |
| `@Web` | Web search results |

Narrower context is more accurate. Naming two or three relevant files usually beats
reaching for `@Codebase` out of habit.

## Project rules

Add `.cursor/rules/` to the repository and the rules apply to every request.

```markdown
---
description: Shared project rules
alwaysApply: true
---

- State management is Zustand. Do not introduce Redux.
- Component files are PascalCase; hooks use the `use` prefix.
- Styling is Tailwind utilities only. Do not create new CSS files.
```

## Compared to the others

| | Copilot | Cursor | Claude Code |
|---|---|---|---|
| Form | Extension | Editor | Terminal / extension |
| Strength | Inline suggestion speed | Editing UX, diff review | Autonomous multi-file work |
| Switching cost | None | Low (settings import) | None (keep your editor) |

If you must pick one, pick by the shape of your work. Heavy typing favours Copilot;
wanting to eyeball every diff favours Cursor; handing over whole tasks favours Claude
Code.

## A caution

Cursor trails VS Code releases, so the newest VS Code features can arrive late. Worth
checking if your team has a standard editor.

## Next

For an agent that makes you write the spec first → [Kiro](/docs/ai/kiro/)
