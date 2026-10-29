---
weight: 110900
title: "HearHear"
description: "A Discord bot that joins a voice channel on command, records each speaker to a separate stream, and hands back a Whisper transcript per person — not just one blended track."
icon: "record_voice_over"
date: "2026-10-29"
lastmod: "2026-10-29"
draft: false
---

Meeting-recorder bots usually give you one audio blob and one transcript, with
"Speaker 2" guessed after the fact. [HearHear](https://github.com/GoodluckH/HearHear/tree/main/bot)
does the easier, more reliable version of that: Discord already knows who is talking in
a voice channel, so the bot records **one stream per speaker** from the start and
transcribes each one separately — speaker attribution for free, instead of inferred from
voiceprints after the recording ends.

It's a small two-person project (Apache-2.0, per `package.json` — there's no separate
`LICENSE` file in the repo) built on `discord.js` and `@discordjs/voice`, not a polished
product. Worth knowing before you point it at a real meeting.

## What `/start` and `/end` actually do

```text
/start   → bot joins your current voice channel, begins per-speaker recording
/end     → bot leaves, merges each speaker's clips, transcribes, uploads, replies with a link
```

Each speaker's audio streams to its own Ogg/Opus file (`createListeningStream`),
auto-ending after 300 ms of silence and re-segmenting every 30 seconds so a quiet
connection doesn't grow one file forever. On `/end`, same-speaker segments merge, each
file goes to OpenAI's `whisper-1` endpoint directly over REST (no SDK, just `fetch` and
`FormData`), and the `.ogg` plus its `.txt` transcript upload to S3 under
`{guild}/{channel}/{date}/`. The bot's own reply links to a dashboard —
`hearhear.vercel.app/dashboard/guilds/<id>` — to read the result; that web half isn't in
the `bot/` folder this page covers, and the link isn't independently verified here.

## Run it yourself

```bash
cd bot
npm install
cp .env.example .env     # TOKEN, CLIENT_ID, AWS keys, S3 bucket, OPENAI_API_KEY
npm start                 # builds, then runs dist/bot.js
```

The bundled `bot/README.md` is stale — it's leftover boilerplate from the
`@discordjs/voice` recorder example this was built on top of and references a
`config.example.json` that doesn't exist in this checkout. The real configuration is
`.env`, read straight from `bot/src/bot.ts` via `dotenv`; trust `.env.example` over the
prose.

You'll need, minimum: a Discord bot token and client ID with `GUILD_VOICE_STATES` and
`GUILD_MESSAGES` intents, an S3 bucket plus an IAM key that can `PutObject`, and an
OpenAI API key for Whisper. Transcription calls are throttled to 50/minute per run
(`p-throttle`), which matters once more than a couple of people are talking.

## Worth knowing before you trust it with a real meeting

- **Local disk first, cloud second.** Recordings land in `./recordings/<guildId>/`
  before upload — the process needs write access and enough disk for a full meeting's
  audio per guild, and a crash between recording and upload leaves files only on the
  machine running the bot.
- **One OpenAI key pays for every transcription**, with no budget cap in the code beyond
  the request throttle — size that against how many servers and how many simultaneous
  meetings you expect.
- **It's a small, not-actively-polished project.** The `/end` handler's own reply still
  carries a `// TODO: change the link` comment in the source. Read `bot/src/` before
  deploying it anywhere that matters; it's short enough to actually do that.

Pairs with [OpenWhispr](/docs/ai-media/openwhispr/) or [VoiceStudio
](/docs/ai-media/voicestudio/) if what you actually want is dictation or local
transcription rather than a Discord-specific meeting bot.

## Next

To write down what you automated → [Linux Desktop](/docs/linux-desktop/)
