---
weight: 2060
title: "hyperfine"
description: "Benchmarking a command properly — many runs, warmup, statistics, and a straight answer about which is faster."
icon: "speed"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

`time ./build.sh` gives you one number from one run, on a machine that was also doing
other things. Run it twice and you get two different numbers, and now you're guessing.
[hyperfine](https://github.com/sharkdp/hyperfine) runs the command many times, warms the
cache first, and reports a mean with a deviation — so "the new version is faster" becomes
a claim you can actually support.

## Install

```bash
brew install hyperfine
cargo install hyperfine
apt install hyperfine          # or: winget install hyperfine
```

## Use it

```bash
hyperfine 'npm run build'
```

```
Benchmark 1: npm run build
  Time (mean ± σ):      2.834 s ±  0.061 s    [User: 3.402 s, System: 0.428 s]
  Range (min … max):    2.761 s …  2.943 s    10 runs
```

Two commands, and it compares them for you:

```bash
hyperfine 'grep -r TODO .' 'rg TODO'
```

The summary ends with the line worth reading — *"rg TODO ran 8.42 ± 0.71 times faster
than grep -r TODO ."* — which is the sentence you wanted in the first place.

## The flags that make a benchmark honest

| Flag | Why |
|---|---|
| `--warmup 3` | Discard the first runs so you measure the code, not a cold file cache |
| `--prepare 'make clean'` | Reset state before **each** run — essential for build benchmarks |
| `--min-runs 20` | More samples when the deviation is wide |
| `-i` | Keep going when the command exits non-zero |
| `--shell=none` | Skip the shell, for commands fast enough that the shell startup dominates |
| `--export-markdown bench.md` | A table you can paste into the pull request |
| `--export-json bench.json` | Machine-readable, for tracking over time |

`--prepare` is the one people miss. Benchmarking an incremental build without resetting
measures the second run's do-nothing path, and the numbers look wonderful right up until
CI disagrees.

## Sweeping a parameter

```bash
hyperfine --warmup 2 -L threads 1,2,4,8 './process --threads {threads}'
hyperfine -L compiler gcc,clang '{compiler} -O2 main.c -o main'
```

One command, one table, the whole curve — the fastest way to find out where more threads
stop helping.

## Reading the result

- **Compare the deviation, not just the mean.** Two means a few percent apart with wide
  spreads are the same number wearing different hats.
- **Change one thing.** Benchmarking a new machine and a new version at once tells you
  nothing about either.
- **Close everything else,** or accept the noise. A laptop thermal-throttling mid-run
  produces a beautiful, meaningless graph.
- **Benchmark what users wait for.** A 40% win on a step that takes 200ms of a two-minute
  build is not a win worth a refactor.

Where it pays off: proving a script change helped before you commit it, choosing between
two libraries with your own workload rather than someone's blog post, and catching the
day a build got slow — export JSON on every release and the regression shows up as a
number instead of a complaint.

## Next

Travel is fast; now the tools for finding things once you arrive →
[ripgrep](/docs/files/ripgrep/)
