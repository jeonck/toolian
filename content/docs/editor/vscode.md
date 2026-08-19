---
weight: 4010
title: "VS Code"
description: "The safest default. The settings and extensions worth touching in the first 30 minutes."
icon: "code"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

VS Code is language-agnostic and filled in with extensions. Settings are easy to share
with a team, and most tutorials are written against it, which makes it a good first
editor.

## Install

```bash
brew install --cask visual-studio-code
winget install Microsoft.VisualStudioCode
```

To use `code .` from a terminal, run
`Shell Command: Install 'code' command in PATH` from the command palette
(`⌘⇧P` / `Ctrl+Shift+P`).

## Six shortcuts to learn first

| Action | macOS | Windows/Linux |
|---|---|---|
| Command palette | `⌘⇧P` | `Ctrl+Shift+P` |
| Quick open a file | `⌘P` | `Ctrl+P` |
| Go to symbol | `⌘⇧O` | `Ctrl+Shift+O` |
| Search everywhere | `⌘⇧F` | `Ctrl+Shift+F` |
| Multi-cursor (same word) | `⌘D` repeatedly | `Ctrl+D` |
| Move a line | `⌥↑` / `⌥↓` | `Alt+↑` / `Alt+↓` |

Knowing the command palette alone lets you find everything else by name.

## First settings to change

`⌘⇧P → Preferences: Open User Settings (JSON)`:

```json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": { "source.fixAll": "explicit" },
  "editor.rulers": [100],
  "editor.bracketPairColorization.enabled": true,
  "files.trimTrailingWhitespace": true,
  "files.insertFinalNewline": true,
  "explorer.compactFolders": false,
  "workbench.editor.enablePreview": false,
  "terminal.integrated.fontFamily": "JetBrainsMono Nerd Font"
}
```

- `formatOnSave` is the single biggest thing you can do to eliminate formatting nits
  from code review.
- `enablePreview: false` stops a tab from being replaced every time you click a file.

## Keep extensions minimal

| Extension | Purpose |
|---|---|
| **ESLint / Prettier** | JS and TS linting and formatting |
| **Python (Microsoft)** | Python language server and debugger |
| **Error Lens** | Shows errors inline on the offending line |
| **GitLens** | Line-level blame and history |
| **EditorConfig** | Applies shared indentation rules |
| **Docker** | Dockerfile support and container management |

More extensions means slower startup. Check `Developer: Show Running Extensions`
periodically and remove what you don't use.

## Sharing settings with a team

Commit `.vscode/settings.json` and `.vscode/extensions.json` and everyone who opens
the repository gets the same formatting rules and extension recommendations.

```json
// .vscode/extensions.json
{ "recommendations": ["dbaeumer.vscode-eslint", "esbenp.prettier-vscode"] }
```

## Remote development

The `Remote - SSH` extension lets you edit code on a server as if it were local. To
develop inside a container, use `Dev Containers` with a
`.devcontainer/devcontainer.json`. Both cut down sharply on environment-drift problems.

## Next

If you'd rather edit without leaving the keyboard → [Neovim](/docs/editor/neovim/)
