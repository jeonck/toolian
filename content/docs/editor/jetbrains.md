---
weight: 4030
title: "JetBrains IDEs"
description: "Getting the most out of the refactoring and debugging in IntelliJ, PyCharm, and GoLand."
icon: "build"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

JetBrains IDEs understand code as **structure**, not text. That makes renaming,
extracting methods, and tracing usages exact. The advantage is largest on big projects
and statically typed languages.

## Picking a product

| Product | Primary language |
|---|---|
| IntelliJ IDEA | Java, Kotlin, Scala |
| PyCharm | Python |
| GoLand | Go |
| WebStorm | JavaScript, TypeScript |
| DataGrip | SQL, databases |
| Rider | .NET, C# |

If you move between many languages, IntelliJ IDEA Ultimate plus plugins covers most of
it. Install and manage versions through **JetBrains Toolbox**.

```bash
brew install --cask jetbrains-toolbox
```

## Core shortcuts

| Action | macOS | Windows/Linux |
|---|---|---|
| Search everywhere | `Shift` twice | `Shift` twice |
| Find action | `⌘⇧A` | `Ctrl+Shift+A` |
| Go to declaration | `⌘B` | `Ctrl+B` |
| Find usages | `⌥F7` | `Alt+F7` |
| Rename | `⇧F6` | `Shift+F6` |
| Refactor menu | `⌃T` | `Ctrl+Alt+Shift+T` |
| Quick fix | `⌥⏎` | `Alt+Enter` |
| Recent files | `⌘E` | `Ctrl+E` |

`⌥⏎` (Alt+Enter) is the all-purpose key that fixes whatever the IDE has flagged.
When you see a red or yellow squiggle, press it.

## Using the refactorings

| Refactoring | When |
|---|---|
| Rename | The name no longer matches what it does (all references update) |
| Extract Method | A function no longer fits on screen |
| Extract Variable | A condition has grown hard to read |
| Change Signature | Adding or reordering parameters (call sites update) |
| Inline | Removing a variable or function used exactly once |

Unlike find-and-replace, none of these touch the same word inside a string or comment.

## The debugger

Set a breakpoint, run, and inspect state directly. The features worth knowing:

- **Conditional breakpoints**: right-click the breakpoint → `Condition`, e.g. `i == 42`
- **Evaluate Expression** (`⌥F8`): run arbitrary code while paused
- **Watch**: keep an eye on an expression
- **Drop Frame**: rewind out of the call you just entered and run it again

Generally faster than `print` debugging.

## When it feels heavy

- `Help → Change Memory Settings` and raise the heap to 2–4 GB.
- Exclude build output from indexing (right-click →
  `Mark Directory as → Excluded`).
- Disable plugins you don't use. Plenty of bundled ones go untouched.

## Next

Close out with the skills that survive any editor →
[Editing Skills](/docs/editor/editing-skills/)
