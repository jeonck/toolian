---
weight: 5049
title: "Needle"
description: "An 8–29 MB foundation model that does tool calls, typed extraction and embeddings on phones, robots and microcontrollers — with a confidence score you can route on."
icon: "memory_alt"
date: "2026-09-20"
lastmod: "2026-09-20"
draft: false
---

[Slotstream](/docs/ai/slotstream/) and [edge0](/docs/ai/edge0/) shrink the *memory* a big
model needs. [Needle](https://github.com/cactus-compute/needle) goes the other way and
shrinks the model: the whole thing is a single **8–29 MB binary**, small enough to ship
inside an app or flash onto a microcontroller.

It buys that size by refusing a job. Needle is explicitly **not a chatbot** — general
conversation is the capacity that was traded away. What it keeps is the part an
application actually needs:

- **Tool calls.** Given the functions your app exposes, it picks the right ones and fills
  every argument from what the user said. Ask for two things and you get two calls in
  order; ask for something no tool covers and you get an **empty list, not a guess**.
- **Structured extraction.** Declare a shape, hand it messy text, get typed fields back.
  A byte-level grammar compiled from your schema constrains every token, so **the output
  parses by construction** — no retry loop around a malformed JSON blob.
- **Embeddings.** The same model returns a sentence vector, so search, matching and
  routing stay on the device.

Apache-2.0, weights on Hugging Face, `pip install cactus-needle`.

## The smallest useful example

```python
import needle

@needle.tool
def get_weather(city: str):
    "Get the current weather for a city."
    return {"city": city, "temp_c": 27, "sky": "clear"}

agent = needle.Needle(tools=[get_weather])
print(agent.run("what's it like in Lagos right now?")["results"])
```

The signature supplies argument types, the docstring is the tool description, and `run()`
closes the loop by executing your function. Every turn returns one JSON object with
`function_calls`, the model's `reasoning`, and a **calibrated confidence**.

## Where this is actually worth using

The size is the headline, but the shape of the thing decides the use cases. Six that fit
it well:

**1. Voice or text commands inside an app, offline.** Speech in, function out, no round
trip. The user is in a lift, on a plane, in a basement, and "set a timer for the pasta"
still works. Pair it with on-device dictation like
[OpenWhispr](/docs/ai-media/openwhispr/): the transcriber produces text, Needle turns it
into the call.

**2. Camera → typed record, without uploading the document.** Photograph a receipt, an
invoice, a prescription, a business card; OCR it on device; hand the mess to Needle with
a schema and get back fields that parse. The whole pipeline stays on the phone, which for
medical, financial or HR documents is the difference between shippable and not.

**3. Notification and message triage.** Extraction generalises to classification — enums
in the schema do the work. Urgent or not, which project, which folder. This is private by
construction: nothing goes to a server to be sorted.

**4. Robots, smart home, automotive, microcontrollers.** An intent layer where there is no
network, no room for a gigabyte, and no tolerance for latency. Weights are 2.125 bits
each in the `.cact` format, mapped and read in place; the per-platform engines are under
1 MB.

**5. Local semantic search and routing.** The embedding endpoint means an app can match a
query against its own content — notes, products, settings, past orders — with no vector
service and no network.

**6. Air-gapped and regulated deployments.** A model that never calls anything is much
easier to get through review than a model with a well-argued privacy policy. WASI and
air-gapped setup are documented targets.

## Confidence is the feature to design around

Every response carries a calibrated score from a learned head, and that changes what a
small model is allowed to do. The pattern the project documents is three-way routing:

| Confidence | Do |
|---|---|
| High | Act — execute the call |
| Middle | Confirm — show the user what you're about to do |
| Low | Refuse — hand off, or ask a question |

Any small model put in front of real actions needs this gate. It's the difference between
"the assistant occasionally does the wrong thing" and "the assistant occasionally asks".
Combined with the empty-list behaviour on out-of-scope requests, it's a more honest
failure mode than most much larger models offer.

## Fine-tuning is the intended path, not an advanced option

Needle's capacity is a **ladder**: every depth from 2 to 20 layers is a deployable model.
The intended workflow is to tune a small subnetwork on your product's own tools and ship
that, rather than shipping the full model.

```sh
pip install "cactus-needle[train]"
needle finetune data.jsonl --epochs 10 --out adapter.safetensors
needle build --lora adapter.safetensors --layers 8 --out tuned.cact

needle build --platform linux-arm64 --layers 8 --out ./pi
./macos-arm64/needle --model needle3.cact --tools tools.json --serve
```

The project reports fine-tuning on DroidCall lifting every subnetwork by 18–36 points,
with tuned subnetworks from 4 layers up passing much larger models — starting at 29M
parameters. Local tuning trains and exports at 4 bits; the 2-bit pipeline behind the
shipped weights runs on the vendor's platform.

Read this as the design intent: **a model that only knows your app's twelve functions can
be very small and very good at exactly that.**

## Two things to check before shipping

**Telemetry is on by default in the binary.** For a model whose entire pitch is that data
stays on the device, that deserves a line in your build script rather than a footnote:

```sh
export NEEDLE_TELEMETRY=0
export DO_NOT_TRACK=1
```

**The benchmarks are vendor-run.** The claims — beating models 10× its size on mobile
tool calls, matching 2–3× bigger ones on extraction — come with published test splits and
metrics, which is better than most, but no independent replication. Your tools are not
their test set; measure on your own before committing a release to it.

## Next

An agent with no subscription and no API key, and the trade that pays for it →
[Freebuff](/docs/ai/freebuff/)
