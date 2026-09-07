---
weight: 5105
title: "Remotion"
description: "Making real MP4 files out of React components — the framework HyperFrames is a reaction to."
icon: "movie"
date: "2026-09-07"
lastmod: "2026-09-07"
draft: false
---

[HyperFrames](/docs/ai/hyperframes/) is inspired by Remotion, and the two work the same
way underneath: render a web page with headless Chrome, hand the frames to FFmpeg, get an
MP4. The difference is the **authoring model**. Remotion's bet is React components;
HyperFrames' bet is plain HTML that humans and agents can both write easily.

If your team already writes React, Remotion is the mature end of this idea — years of
production use, a studio UI, a player component, and serverless rendering.

## Start a project

```bash
npx create-video@latest
cd my-video
npm run dev          # opens Remotion Studio
```

You need Node and Git; you do **not** need to install FFmpeg or Chrome. Since v4,
Remotion downloads FFmpeg and a Chrome Headless Shell into `node_modules` on first use.

## The whole idea in one hook

A component is drawn once per frame, and `useCurrentFrame()` tells it which frame it is.
Animation is therefore just a function of a number — no timeline object, no playback
state.

```tsx
import {AbsoluteFill, useCurrentFrame, useVideoConfig, interpolate, spring} from 'remotion';

export const Title: React.FC<{text: string}> = ({text}) => {
  const frame = useCurrentFrame();
  const {fps} = useVideoConfig();

  const opacity = interpolate(frame, [0, 20], [0, 1], {extrapolateRight: 'clamp'});
  const y = spring({frame, fps, config: {damping: 200}});

  return (
    <AbsoluteFill style={{justifyContent: 'center', alignItems: 'center'}}>
      <h1 style={{opacity, transform: `translateY(${(1 - y) * 40}px)`}}>{text}</h1>
    </AbsoluteFill>
  );
};
```

Compositions are registered once, with their dimensions, frame rate, and length:

```tsx
<Composition
  id="Title"
  component={Title}
  durationInFrames={150}
  fps={30}
  width={1920}
  height={1080}
  defaultProps={{text: 'Launch day'}}
/>
```

`<Sequence from={90}>` shifts a child's sense of frame 0, which is how scenes get
arranged in time.

## Render it

```bash
npx remotion studio                       # preview, scrub, tweak props
npx remotion render Title out/title.mp4
npx remotion render Title out/title.mp4 --props='{"text":"v2.0 is out"}'
```

Because a composition takes props, one component renders a hundred variants — per
customer, per release, per language — from the same build. That's the case Remotion is
really built for, and it's why the render command takes JSON.

For scale, `@remotion/lambda` splits frames across AWS Lambda functions
(`npx remotion lambda render`, concurrency up to 200); `@remotion/player` embeds the same
composition in your own React app as an interactive preview, so a user can adjust the
text and see the result before you spend a render on it.

## The licence is the thing to check first

Remotion is source-available, not open source.

| You are | Terms |
|---|---|
| An individual | Free, including commercial work |
| A for-profit company with up to 3 employees | Free |
| A non-profit | Free |
| Evaluating it, not yet commercially | Free |
| A for-profit company with 4 or more employees | **Company licence required**, priced per seat at [remotion.pro](https://www.remotion.pro/license) |

Selling your own derivative of Remotion is not permitted at any tier. This catches teams
out at exactly the wrong moment, so settle it before the video pipeline is load-bearing.

## Remotion or HyperFrames

| | Remotion | [HyperFrames](/docs/ai/hyperframes/) |
|---|---|---|
| You write | React components | HTML, CSS, and a paused timeline |
| Licence | Source-available; paid above 3 employees | Apache 2.0 |
| Ecosystem | Large — players, templates, Lambda, GPU guides | Young, agent-first |
| Best when | The team lives in React and wants parametrised video at scale | An agent authors the file, or the video should diff like a document |
| Setup | Node and Git; FFmpeg and Chrome auto-installed | Node 22+, FFmpeg, and a Chromium you supply |

Both turn "make the release video" into a build step. Pick on the licence and on which
source format your team — or your agent — will actually maintain.

## Next

An agent is only as useful as what it can reach → [Composio](/docs/ai/composio/)
