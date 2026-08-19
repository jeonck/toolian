---
weight: 3050
title: "jq"
description: "Pull values out of API responses and config files, and reshape them."
icon: "data_object"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

If you have ever scanned an API response by eye and missed the field you needed, `jq`
is the answer. It pretty-prints JSON, extracts the parts you want, and reshapes them.

## Install

```bash
brew install jq
sudo apt install jq
winget install jqlang.jq
```

## Basics

```bash
# pretty-print
curl -s https://api.github.com/repos/jeonck/toolian | jq

# one field
jq '.name' repo.json

# a nested field
jq '.owner.login' repo.json

# iterate an array
jq '.[] | .name' repos.json

# build a new object
jq '.[] | {name, stars: .stargazers_count}' repos.json
```

## Filters worth knowing

| Filter | Meaning |
|---|---|
| `.` | The whole input |
| `.foo` | A field |
| `.foo?` | null instead of an error when missing |
| `.[]` | Iterate an array or object |
| `.[2:5]` | Array slice |
| `\|` | Pipe one filter into the next |
| `select(cond)` | Keep matching items |
| `map(expr)` | Apply to each array element |
| `length` | Length |
| `keys` | Key list |
| `sort_by(.field)` | Sort |
| `group_by(.field)` | Group |
| `-r` | Raw output, no quotes |

## In practice

```bash
# names of repos with more than 100 stars
jq -r '.[] | select(.stargazers_count > 100) | .name' repos.json

# array to CSV
jq -r '.[] | [.name, .language, .stargazers_count] | @csv' repos.json

# count by status code
jq -r '.[].status' logs.json | sort | uniq -c | sort -rn

# edit a value into a new file
jq '.version = "2.0.0"' package.json > tmp && mv tmp package.json

# merge two files
jq -s '.[0] * .[1]' base.json override.json
```

## Using it safely

- **Never overwrite in place**: `jq ... file.json > file.json` truncates the file. Use
  a temp file, or `sponge` from moreutils.
- **Missing keys**: `.a.b` errors when `a` is absent. Guard with `.a?.b?` or
  `// "default"`.
- **Debugging**: for a long filter, run it one pipe stage at a time — you'll find the
  culprit much faster.

## For YAML too

```bash
brew install yq
yq '.services.web.image' docker-compose.yml
yq -o=json '.' config.yaml | jq
```

## Next

With files under control, on to where you write the code →
[Editors & IDEs](/docs/editor/)
