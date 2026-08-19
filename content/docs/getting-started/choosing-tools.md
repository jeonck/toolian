---
weight: 1010
title: "Choosing a Tool"
description: "Six questions worth five minutes before you install anything new."
icon: "checklist"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Adding tools is not productivity by itself. Each one costs installation, configuration,
learning, and maintenance, and the more you have, the more you have to explain to your
team. Run through these six questions before adopting anything and you'll avoid most
of the regret.

## Six questions

| Question | Passing answer |
|---|---|
| How often do I do this? | At least three times a day, or 30+ minutes even once a week |
| Can my current tools do it? | If an unused feature of something you already run covers it, start there |
| What's the learning cost? | If you get a first win within 30 minutes, it passes |
| Is anyone else forced into it? | A tool only you use is far easier to adopt than a team standard |
| Can I get back out? | Can the data be exported as text, Markdown, or JSON? |
| Is the project alive? | A release in the last six months, and issues that get replies |

## Suggested order of adoption

1. **Personal tools first.** Terminal, search, editor — things that start and end with
   you. Cheap to reverse, and you feel the benefit daily.
2. **Team tools next.** CI, linters, hooks — anything that affects other people. Prove
   it as a personal tool before proposing it.
3. **Platform tools last.** Orchestration and IaC carry the highest cost to undo.

## Common traps

- **Configuration perfectionism.** Instead of spending two hours on a prompt theme,
  run the defaults for a week and fix only what actually annoyed you.
- **Leaving duplicates around.** Two tools doing the same job means you will never
  remember which config you edited. Once a new one sticks, delete the old one.
- **Undocumented aliases.** A pile of aliases only you know leaves you helpless on
  anyone else's machine. Scripts you share should use the real commands.

## Next

With a bar in place, move on to
[Package Managers](/docs/getting-started/package-managers/) and build an environment
where the install commands in the rest of this site just work.
