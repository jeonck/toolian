---
weight: 4040
title: "Editing Skills"
description: "Multi-cursor, regex replace, LSP, and EditorConfig — the parts that survive changing editors."
icon: "edit_note"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

You change editors every few years; these techniques come with you. They have the most
reliable return on investment of anything here.

## Multi-cursor

For repeating the same edit across several lines.

| Action | VS Code | JetBrains |
|---|---|---|
| Add next occurrence | `⌘D` / `Ctrl+D` | `⌃G` / `Alt+J` |
| Select all occurrences | `⌘⇧L` / `Ctrl+Shift+L` | `⌃⌘G` / `Ctrl+Alt+Shift+J` |
| Add cursor above/below | `⌥⌘↑↓` / `Ctrl+Alt+↑↓` | `⌥⌥↑↓` (Alt twice) |
| Add cursor anywhere | `⌥click` / `Alt+click` | Same |

Typical uses: quoting ten list items at once, or fixing several import statements
simultaneously.

## Regex find and replace

Patterns beyond what multi-cursor can handle go faster with a regex. Turn on the `.*`
icon in the search box and reference capture groups with `$1`, `$2`.

| Goal | Find | Replace |
|---|---|---|
| Swap call arguments | `move\((\w+), (\w+)\)` | `move($2, $1)` |
| `var` to `const` (declarations only) | `\bvar (\w+) =` | `const $1 =` |
| Strip console logs | `^\s*console\.log\(.*\);?\n` | (empty) |
| Normalise quotes | `'([^']*)'` | `"$1"` |

**Careful**: always preview how many matches will change, and do it with a clean
working tree so undoing is easy.

## Understanding LSP

The Language Server Protocol separates "knowledge of a language" from the editor.
Completion, go-to-definition, and live error reporting all come from a language server;
the editor only draws the result.

- VS Code extensions install language servers automatically.
- Neovim manages them with `nvim-lspconfig` and `mason.nvim`.
- Because several editors share the same server (`gopls`, `pyright`,
  `typescript-language-server`), switching editors barely changes the quality of your
  code intelligence.

When something misbehaves, read the language server log first. Usually the project root
was detected wrongly or dependencies aren't installed.

## Pinning team rules with EditorConfig

Even with everyone on different editors, indentation and newlines can be unified. Put
`.editorconfig` at the project root.

```ini
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
indent_style = space
indent_size = 2

[*.py]
indent_size = 4

[*.md]
trim_trailing_whitespace = false
```

Most editors support it natively or with one extension. It removes a whole class of
noisy-diff arguments.

## Snippets

If you type the same block repeatedly, register it. In VS Code,
`⌘⇧P → Snippets: Configure Snippets`:

```json
{
  "React function component": {
    "prefix": "rfc",
    "body": [
      "export function ${1:Name}() {",
      "  return <div>$0</div>;",
      "}"
    ]
  }
}
```

`$1` is the first cursor stop, `$0` the last.

## Next

With editing in hand, layer AI on top → [AI Coding Tools](/docs/ai/)
