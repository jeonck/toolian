---
weight: 5170
title: "DeepEval"
description: "Pytest for LLM applications — 50+ research-backed metrics, thresholds that fail the build, and a judge you can point at your own criteria."
icon: "checklist"
date: "2026-09-12"
lastmod: "2026-09-12"
draft: false
---

[promptfoo](/docs/ai/promptfoo/) gives you a matrix. [DeepEval
](https://github.com/confident-ai/deepeval) gives you a **test suite** — and if your team
already believes a red pytest run blocks a merge, that belief transfers to model quality
for free.

Python 3.9+:

```bash
pip install -U deepeval
```

## A first test

```python
import pytest
from deepeval import assert_test
from deepeval.metrics import GEval
from deepeval.test_case import LLMTestCase, SingleTurnParams

def test_case():
    correctness_metric = GEval(
        name="Correctness",
        criteria="Determine if the 'actual output' is correct based on the 'expected output'.",
        evaluation_params=[SingleTurnParams.ACTUAL_OUTPUT, SingleTurnParams.EXPECTED_OUTPUT],
        threshold=0.5,
    )
    test_case = LLMTestCase(
        input="What if these shoes don't fit?",
        actual_output="You have 30 days to get a full refund at no extra cost.",
        expected_output="We offer a 30-day full refund at no extra costs.",
    )
    assert_test(test_case, [correctness_metric])
```

```bash
export OPENAI_API_KEY="..."
deepeval test run test_chatbot.py
```

Every metric scores 0–1, `threshold` decides pass or fail, and `deepeval test run` is a
real pytest integration — so the build goes red when quality drops, in the same place
your other tests already do.

**`GEval` is the one to learn first.** You describe the criterion in a sentence of plain
English and it grades against that, which covers the enormous class of checks that are
obvious to a human and impossible to regex.

## What's in the box

50+ metrics, all runnable locally: RAG (faithfulness, contextual precision and recall),
agents and tool use, multi-turn conversation, safety, and multimodal. Trajectory-based
evaluation via `evals_iterator()` scores the **path** an agent took — the ordered
sequence of decisions and tool calls — not just its final answer, which is where agent
bugs actually live.

Any model can be the judge, including a local one through [Ollama](/docs/ai/ollama/) —
useful when you'd rather not send production data to a third party just to grade it.

## What it costs

The framework is free and open source, and local metric evaluation stays local.
`deepeval login` connects it to **Confident AI**, the commercial platform, for shareable
reports and team dashboards — optional, and worth knowing it's optional, because the
README recommends it warmly enough that people assume otherwise.

The bill that *does* arrive is judge tokens. LLM-as-a-judge means every test case is one
or more extra model calls; a thorough suite on every commit adds up faster than people
plan for. Run the full sweep nightly and a fast subset per commit.

## Next

Tests tell you what broke before release. For what's happening after it →
[Langfuse](/docs/ai/langfuse/)
