---
weight: 11155
title: "LiveKit"
description: "The real-time voice agent stack — LiveKit for transport, plus STT, LLM, TTS, and telephony — with what each piece costs and which ones will sign a HIPAA BAA."
icon: "graphic_eq"
date: "2026-09-13"
lastmod: "2026-09-13"
draft: false
---

A voice agent is four vendors pretending to be one product: something to carry audio in
real time, something to turn speech into text, a model to think, and something to turn
text back into speech. Add a phone number and it's five. [LiveKit](https://livekit.com/)
is the piece in the middle — open-source WebRTC transport plus an agents framework — and
LiveKit Cloud sells the other four through one bill, which is why it's the pick here.

## LiveKit Cloud

| Plan | Price | Included | BAA |
|---|---|---|---|
| Build | $0 | 1,000 agent minutes, 5 concurrent sessions, 50 US inbound minutes | No |
| Ship | $50/mo | 5,000 agent minutes, 20 concurrent | No |
| Scale | $500/mo | 50,000 agent minutes, up to 600 concurrent | No |
| Enterprise | Custom | — | **Signed BAA here only** |

Overage is **$0.01/min** for agent sessions and US inbound calls on every plan; SIP
trunking from a third-party carrier is $0.004/min on Ship and $0.003/min on Scale.

That BAA column is the one to read twice. Scale at $500 is not HIPAA-eligible; if you
handle PHI, budget for an Enterprise conversation from day one, or self-host the
open-source server and take the compliance on yourself.

## LiveKit Inference

Per-minute prices for the models LiveKit resells, so a whole turn is one line item:

| Layer | Model | Build → Scale price |
|---|---|---|
| STT | Deepgram Nova-3 | $0.0048 → $0.0042/min (multilingual ~$0.0058) |
| STT | AssemblyAI Universal-Streaming | $0.0025/min |
| LLM | OpenAI GPT-4o mini | $0.0006/min |
| LLM | Gemini Flash Lite | $0.0010/min |
| TTS | Cartesia Sonic 3 | $0.0300 → $0.0225/min |
| TTS | Inworld Realtime TTS Mini | $0.0090 → $0.0048/min |

Rough total for a cheap stack: about **$0.02–0.05 per agent-minute** all in, dominated by
TTS. The LLM is a rounding error at these rates; the voice is not.

## Going direct instead

Inference is a convenience markup. The same vendors sell to you directly, and going
direct is what you'll do the moment you need a BAA, since LiveKit's only covers LiveKit.

| Vendor | What you'd pay direct | HIPAA / BAA |
|---|---|---|
| Deepgram | $200 free credit, no minimum; Nova-3 $0.0048/min mono, $0.0058 multi; Growth from ~$4K/yr | Enterprise only |
| [ElevenLabs](/docs/ai-media/elevenlabs/) | Starter $6 · Creator $22 · Pro $99; Flash ~$0.05/1K chars | Enterprise only |
| OpenAI | Realtime/speech-to-speech ~$0.05/min; text models per-token | Direct: no. **Azure OpenAI: yes**, under Microsoft's BAA |
| Azure Speech | STT $1/hour, Neural TTS $15/1M chars; 5 hours + 500K chars free monthly | Yes, Microsoft BAA |
| Twilio | Number $1.15/mo, inbound $0.0085/min, SIP $0.004/min | On request, eligible products only |
| [Supabase](/docs/vibe-infra/supabase/) | Pro $25 | Team $599 — or use RDS/Postgres you control |

Two patterns fall out of that table. **For a hobby or startup product**, LiveKit Cloud
Build/Ship plus Inference is the shortest path and the cheapest bill. **For anything
regulated**, the stack rearranges around who will sign: Azure for the model and speech,
Deepgram or Azure for STT, Twilio for the phone line, your own Postgres for data, and
LiveKit Enterprise or self-hosted in the middle.

Prices are as of September 2026 and every one of these vendors reprices quarterly —
check before you put a number in a proposal.

## First agent

```bash
pip install "livekit-agents[openai,deepgram,cartesia,silero]"
export LIVEKIT_URL=wss://<project>.livekit.cloud
export LIVEKIT_API_KEY=... LIVEKIT_API_SECRET=...
```

```python
from livekit.agents import Agent, AgentSession, JobContext, WorkerOptions, cli
from livekit.plugins import deepgram, openai, cartesia, silero

async def entrypoint(ctx: JobContext):
    session = AgentSession(
        stt=deepgram.STT(model="nova-3"),
        llm=openai.LLM(model="gpt-4o-mini"),
        tts=cartesia.TTS(),
        vad=silero.VAD.load(),
    )
    await session.start(agent=Agent(instructions="Be brief."), room=ctx.room)

if __name__ == "__main__":
    cli.run_app(WorkerOptions(entrypoint_fnc=entrypoint))
```

```bash
python agent.py dev          # then talk to it in the LiveKit Agents Playground
```

Swapping a vendor is changing one constructor. That is the practical reason to build on
LiveKit even if you never use Inference: the compliance-driven rearrangement above is a
four-line diff, not a rewrite.

## Next

The infrastructure works. Now get paid →
[Lemon Squeezy](/docs/vibe-infra/lemon-squeezy/)
