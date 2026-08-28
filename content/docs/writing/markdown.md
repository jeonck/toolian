---
weight: 10030
title: "Markdown"
description: "The syntax that works in READMEs, issues, and docs everywhere — and the parts people get wrong."
icon: "article"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Markdown is the common language of GitHub, Slack, Notion, and every static site
generator. Thirty minutes to learn, and you use it for the rest of your career.

## The basics

```markdown
# Heading 1
## Heading 2
### Heading 3

**bold** *italic* ~~strikethrough~~ `inline code`

- a list
- of items
  - nested (two or more spaces)

1. numbered
2. list

> a block quote

[link text](https://example.com)
![alt text](./image.png)

---
```

## Code blocks

Naming the language turns on syntax highlighting.

````markdown
```python
def hello(name: str) -> str:
    return f"Hello {name}"
```
````

To show a code block inside a code block, use four backticks on the outer fence.

## Tables

```markdown
| Tool | Purpose | Platform |
|---|---|---|
| ripgrep | Code search | All |
| fd | Finding files | All |
```

Alignment goes in the separator row, with colons.

```markdown
| Left | Centre | Right |
|:---|:---:|---:|
```

Don't align tables by hand — let an editor plugin (VS Code's Markdown All in One and
friends) do it.

## What people get wrong

| Problem | Cause | Fix |
|---|---|---|
| Line break ignored | A single newline is not a break | Add a blank line, or two trailing spaces |
| List runs into the paragraph | No blank line before it | Blank lines around lists |
| Nesting doesn't nest | Not enough indentation | Two to four spaces (not tabs) |
| A literal `*` appears | Space next to the emphasis marker | Write `**bold**` with no gaps |
| Table doesn't render | Missing separator | The `\|---\|` row is mandatory |
| A `<` disappears | Parsed as HTML | Wrap it in code: `` `<div>` `` |

## GitHub extensions

```markdown
- [ ] to do
- [x] done

@username  #123  a1b2c3d

~~~
> [!NOTE]
> Worth knowing.

> [!WARNING]
> This cannot be undone.
~~~
```

Writing `Closes #123` in a PR body closes that issue on merge.

## Front matter

Static site generators read a metadata block at the very top of the file.

```markdown
---
title: "Page title"
description: "One-line summary"
date: "2026-08-19"
draft: false
---
```

## The shape of a good document

Structure matters more than syntax.

1. **What it is** — in one sentence
2. **Why you'd want it** — the problem it solves
3. **How to start** — commands that work when pasted
4. **What you'll use often** — as a table
5. **When you get stuck** — common problems and fixes

Following that order in a README alone dramatically cuts "I can't get it installed"
messages.

## Checking tools

```bash
# lint the syntax
npm install -g markdownlint-cli2
markdownlint-cli2 "**/*.md"

# find broken links
npm install -g markdown-link-check
markdown-link-check README.md
```

## Next

Structure that resists description is better drawn →
[Mermaid](/docs/writing/mermaid/)
