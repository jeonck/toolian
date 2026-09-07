---
weight: 12065
title: "OmniVoice"
description: "Zero-shot voice cloning and voice design across 600+ languages, running on your own GPU under Apache 2.0."
icon: "record_voice_over"
date: "2026-09-07"
lastmod: "2026-09-07"
draft: false
---

[ElevenLabs](/docs/ai-media/elevenlabs/) does this well and charges for it.
[OmniVoice](https://github.com/k2-fsa/OmniVoice) does it on your own machine, under
Apache 2.0, in **over 600 languages** — the widest coverage of any zero-shot TTS model.
It's from k2-fsa, the team behind sherpa-onnx and Icefall, so the speech-research
pedigree is real.

Three modes, one `generate()` call: clone a voice from a few seconds of audio, *describe*
a voice you want, or let the model pick one.

## Try it before you install anything

The [HuggingFace Space](https://huggingface.co/spaces/k2-fsa/OmniVoice) and the
[Colab notebook](https://colab.research.google.com/github/k2-fsa/OmniVoice/blob/master/docs/OmniVoice.ipynb)
both run it with nothing local. Then:

```bash
# PyTorch first, matched to your hardware — CUDA build, plain wheels for Apple
# Silicon, or Intel's XPU index for Arc
pip install omnivoice
omnivoice-demo --ip 0.0.0.0 --port 8001    # local Gradio UI
```

`omnivoice-infer` does single generations from the shell and `omnivoice-infer-batch`
spreads a JSONL job across multiple GPUs. Behind a firewall, `export
HF_ENDPOINT="https://hf-mirror.com"` fixes the model download.

## Voice cloning

```python
from omnivoice import OmniVoice
import soundfile as sf, torch

model = OmniVoice.from_pretrained("k2-fsa/OmniVoice", device_map="cuda:0",
                                  dtype=torch.float16)   # "mps" / "xpu" also work

audio = model.generate(
    text="Hello, this is a test of zero-shot voice cloning.",
    ref_audio="ref.wav",
    ref_text="Transcription of the reference audio.",
)
sf.write("out.wav", audio[0], 24000)
```

Drop `ref_text` and Whisper transcribes the reference for you. **Use 3–10 seconds of
reference audio** — longer is slower and often worse. Keep the reference in the same
language as the target unless you want the accent to travel with it, because in
cross-lingual cloning it does.

Encoding the reference is the slow part, so do it once:

```python
prompt = model.create_voice_clone_prompt(ref_audio="ref.wav", ref_text="...")
prompt.save("my_voice.pt")

# later
from omnivoice import VoiceClonePrompt
audio = model.generate(text="Hello again!",
                       voice_clone_prompt=VoiceClonePrompt.load("my_voice.pt"))
```

## Voice design — no reference at all

```python
audio = model.generate(
    text="Hello, this is a test of zero-shot voice design.",
    instruct="female, low pitch, british accent",
)
```

Attributes combine freely: gender, age (child to elderly), pitch, whisper style, English
accents, Chinese dialects. **The catch is in the docs and worth repeating: voice design
was trained on Chinese and English only.** It generalises to other languages but gets
unstable on low-resource ones. The 600-language headline belongs to cloning.

## Fine control inside the text

```python
model.generate(text="[laughter] You really got me. I didn't see that coming at all.")
model.generate(text="He plays the [B EY1 S] guitar while catching a [B AE1 S] fish.")
```

Non-verbal tags cover `[laughter]`, `[sigh]`, and a set of question, surprise, and
dissatisfaction sounds. English pronunciation is overridable with CMU dictionary
phonemes, Chinese with pinyin plus tone numbers — so 打折 stops being read as 打 + 折本.

Numbers need help: "123" is read digit by digit unless you pass `normalize_text=True`
(`pip install "omnivoice[tn]"`).

Generation takes `num_step` (32 by default, 16 for speed), `speed`, and `duration` to pin
the output length. The project reports an RTF as low as 0.025 — 40× real time — and
FlashInfer kernels give another 2–2.9× losslessly on CUDA.

## OmniVoice or ElevenLabs

| | OmniVoice | [ElevenLabs](/docs/ai-media/elevenlabs/) |
|---|---|---|
| Runs | Your GPU | Their servers |
| Costs | Electricity | Per character, on a plan |
| Languages | 600+ for cloning | Far fewer, more polished each |
| Licence | Apache 2.0, commercial use fine | Their terms, per tier |
| Needs | A GPU and a Python environment | A browser |
| Best at | Volume, privacy, unusual languages | One clip that has to sound perfect now |

If the audio can't leave your machine, or the language isn't on anyone's product page,
this is the only real option.

## The part that isn't technical

Cloning a voice from ten seconds of audio is trivially easy now, and that is exactly the
problem. The project's own disclaimer prohibits unauthorised cloning, impersonation, and
fraud, and in a growing number of jurisdictions a voice is protected in its own right.

Get explicit permission from the person whose voice you clone, keep it in writing, and
disclose synthetic speech to whoever hears it. "The model let me" has never been a
defence, and for voice it's a bad one.

## Next

The other direction — turning what you say into text →
[OpenWhispr](/docs/ai-media/openwhispr/)
