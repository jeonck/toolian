---
weight: 5160
title: "promptfoo"
description: "Running your prompts as a test matrix from the command line, then pointing 50+ attack categories at the result — MIT licensed, local, and now owned by OpenAI."
icon: "rule"
date: "2026-09-12"
lastmod: "2026-09-12"
draft: false
---

Everything up to here helps an agent write code. Nothing so far tells you whether what it
built actually works — and "it looked fine when I tried it" is not a test.

[promptfoo](https://github.com/promptfoo/promptfoo) is the shortest path out of that. A
YAML file lists your prompts, the models to try, and what a correct answer looks like; one
command runs every combination and prints a matrix. It's MIT licensed, the evals run
**100% locally**, and your prompts never leave the machine.

## Five minutes to a first result

Node 22.22+ (24 LTS recommended), or `brew install promptfoo`, or `pip install promptfoo`:

```sh
npm install -g promptfoo
promptfoo init --example getting-started
cd getting-started

export OPENAI_API_KEY=sk-...
promptfoo eval
promptfoo view
```

`eval` prints the table in the terminal; `view` opens the web viewer for the same run.
Nothing to install if you'd rather not commit — `npx promptfoo@latest` runs any command.

## Assertions, not vibes

The reason a YAML file beats a scratch notebook is that each test case carries an
assertion, and assertions accumulate into a regression suite:

| Kind | Good for |
|---|---|
| exact match, substring, regex | Deterministic outputs, refusal strings, formats |
| JSON schema | Structured output and tool-call arguments |
| semantic similarity | "Close enough" answers that vary in wording |
| LLM-graded rubric | Tone, helpfulness, anything you'd otherwise eyeball |

Mix providers freely — OpenAI, Anthropic, Azure, Bedrock, [Ollama](/docs/ai/ollama/) and
many more — which makes the "is the cheap model good enough here?" question answerable in
one run instead of an afternoon. Caching and live reload keep the loop fast, and the
whole thing drops into CI.

## The red team is the part people stay for

`promptfoo` also generates adversarial inputs against your app and scans for **50+
vulnerability categories** — prompt injection, data exfiltration, jailbreaks, PII leakage
— producing a report rather than a pass/fail. There's also code scanning for
LLM-related security and compliance issues in pull requests.

This matters more the moment your app has tools. An agent that can read files and call
APIs turns a successful injection into an action, not just an embarrassing sentence.

## The ownership question, stated plainly

**OpenAI acquired promptfoo on 9 March 2026.** The stated commitment is that the
open-source tool stays open source and MIT licensed, and it has so far; the technology is
also being folded into OpenAI's enterprise platform. Adoption is real — 350,000+
developers and teams at more than 25% of the Fortune 500.

Worth knowing rather than worrying about: the licence is MIT and the history is public, so
a fork remains possible whatever happens. But a tool that tests models is now owned by a
company that sells models, and if you're using it to choose between vendors, that's a
conflict to keep in view. Read the eval results, not the defaults.

## Next

For the same job as a pytest suite instead of a YAML matrix →
[DeepEval](/docs/ai/deepeval/)
