---
weight: 150775
title: "Fish Audio Drama 3"
description: "A 35B open-weight TTS model that takes director's notes — fix the take with a sentence, swap one word without touching the rest, and voice a whole scripted scene in one pass."
icon: "theater_comedy"
date: "2026-10-22"
lastmod: "2026-10-22"
draft: false
---

Every voice model so far on this page treats a generation as one-shot: you write the
prompt, you get the take, you regenerate from scratch if it's wrong. [Fish Audio
Drama 3](https://github.com/fishaudio/fish-speech) treats a generation as a **draft you
can direct**. Released 13 October 2026, open-weight, 35B parameters (3B active per
token, mixture-of-experts), built on Fish Audio's existing fish-speech stack alongside
their smaller S2-Pro model.

## What's actually new, not just bigger

- **Multi-turn director revision.** Don't like the take? Say "gentler, and a bit
  sadder" and it regenerates *with that note in context* — not a fresh roll from zero.
- **Infilling.** Edit audio the way you edit text: change "Wednesday" to "Thursday" and
  only that word changes, with the surrounding timbre and transitions staying natural.
  S2-Pro, the previous flagship, couldn't do either of these.
- **Scene-style multi-character production.** Hand it a script with several speakers
  and it voices the whole scene in one streamed pass, holding each character's timbre
  stable across a long dialogue rather than re-cloning per line.
- **Inline natural-language tags** — `[whisper]`, `[angry]`, `[laughing]` — and
  noticeably less of the flattening effect where a reference voice washes out real
  crying, laughing, or whispering into a monotone approximation.

Multilingual voice cloning error rates (CER/WER) beat S2-Pro on the public benchmarks
Fish Audio cites (MiniMax Multilingual Testset, Ming-Freeform, Step-Audio-Edit) — their
own numbers, not independently reproduced, so read the
[technical report](https://arxiv.org/abs/2610.18010) rather than the launch blog if the
exact figures matter to your decision.

## Running it yourself needs a real GPU

This is not a laptop model. Linux or WSL, **NVIDIA only** — no CPU, no Apple MPS, no AMD
ROCm — and **48 GB of VRAM minimum for the FP8 weights**. `--compile` isn't supported.
Older, pre-SM80 cards (V100 and earlier) fall back to float16 since they lack bfloat16
support.

```bash
apt install portaudio19-dev libsox-dev ffmpeg

conda create -n fish-speech python=3.12 && conda activate fish-speech
pip install --extra-index-url https://download.pytorch.org/whl/cu129 -e ".[cu129]"
# pick cu126, cu128 or cu129 to match your CUDA toolkit

pip install -U "huggingface_hub[cli]"
hf download fishaudio/drama-3 --local-dir checkpoints/drama-3
```

If that checkpoint folder has no `codec.pth`, Drama 3 falls back to S2-Pro's codec —
grab that too:

```bash
hf download fishaudio/s2-pro --local-dir checkpoints/s2-pro
```

Docker is the faster path if you've got the card:

```bash
docker run --gpus device=0 \
    -v ./checkpoints:/app/checkpoints \
    -e LLAMA_CHECKPOINT_PATH=checkpoints/drama-3 \
    -p 8080:8080 \
    fishaudio/fish-speech:server-cuda
```

API at `localhost:8080`, a WebUI at `localhost:8080/ui`. On anything older than an SM89
card, swap `--gpus device=0` for `--gpus all`. **Fish Audio's own docs recommend
SGLang-Omni over the stock server for anyone who cares about throughput** — see the
[SGLang-Omni README](https://github.com/sgl-project/sglang-omni/blob/main/sglang_omni/models/fishaudio_drama_3/README.md)
before you commit to a serving stack.

No 48 GB card handy: the hosted API and the fish.audio web/iOS/Android apps run the same
model without the hardware, at a metered per-character rate that's had a discounted
launch price through 12 November 2026 — check
[fish.audio](https://fish.audio/)'s own pricing page rather than any third-party
estimate, which is the one number every secondary source quoted differently.

## Read the license before you ship anything

"Open-weight" here is **not** MIT or Apache. The Fish Audio Community License v1.0 is
free for research and non-commercial use, and free for commercial use **only while your
and your affiliates' combined annual revenue stays under US$500,000** — cross that and
you need a separate written license, with a 90-day transition window from the date you
notice.

The sharper trap is **"Hosted Service,"** which the license carves out regardless of
revenue: you may not offer Drama 3 to third parties who supply their own text or voice
and get audio back through an API, SDK, or a website whose primary purpose is
TTS/voice-cloning — that always needs a separate deal, no revenue exemption. Narration
baked into your own product, dubbing you produce and deliver as a studio, or an
assistant speaking its own replies are explicitly **not** a Hosted Service under the
license text; a self-serve "paste your script, get audio" API is. If your product
*is* text-to-speech, read Section III(c) of the [LICENSE](https://github.com/fishaudio/fish-speech/blob/main/LICENSE)
yourself rather than taking this paragraph as legal advice.

## Where it sits

For most everyday narration, dubbing, or dictation work, [VoiceStudio
](/docs/ai-media/voicestudio/) or [OmniVoice](/docs/ai-media/omnivoice/) stay the easier
local answer — no 48 GB card required. Reach for Drama 3 specifically when the job is
**scripted**: a multi-character scene, a line that needs one word fixed without a
re-record, or a performance note delivered in a second pass instead of a new take.

## Next

The other direction — turning what you say into text →
[OpenWhispr](/docs/ai-media/openwhispr/)
