---
layout: article
title: "Advent of Code — A Small Daily Habit to Sharpen Algorithms and Team Energy"
date: 2023-12-01
tags: [algorithms, craftsmanship, coaching, community]
author: Joseph
description: Why I practice Advent of Code every December, how it keeps core algorithmic skills fresh, and how it inspired a tiny internal platform to run Extreme Startup-style katas with colleagues.
---

Every December, Advent of Code (AoC) drops two small programming puzzles each day. They are bite-sized, story‑flavored, and perfectly tuned to fit in a coffee break or an evening session. Over the years, this event has become my favorite way to keep my algorithmic muscles warm and to nudge peers into a friendly, productive competition.

Reality check: keeping up for all 25 days is hard. The difficulty ramps up, and some days jump far outside a comfortable timebox. When that happens, I intentionally keep the workload light and spread the tougher puzzles across the year. The goal is sustainable practice, not a perfect streak.

My solutions and notes live here: https://github.com/caring-coder/adventofcode (use for ideas only—don’t copy the scaffolding; it’s personal and Java‑oriented).

## What makes Advent of Code effective

- Daily cadence, tiny scope: Each day is approachable. Shipping a working solution in ~30–60 minutes is satisfying and creates momentum.
- Two-part design: Part 2 forces you to generalize or optimize the first approach, modeling real-world iteration.
- Immediate feedback: You get an answer check within seconds. Tight feedback loops are addictive and educational.
- Language‑agnostic: Use any stack. Pick the one you want to grow this year.
- Safe playground: You can try data structures you rarely need at work (priority queues, segment trees, union‑find, lazy BFS/DFS variants) without risk.

## Why this is great for teams and motivation

- Shared scoreboard without pressure: A simple leader board keeps things playful and visible.
- Conversation starters: Lunchtime discussions on heuristics, complexity, or edge cases build a culture of engineering curiosity.
- Practicing the fundamentals: Arrays, maps, sets, recursion, graph traversal, parsing, dynamic programming—these fundamentals pay off everywhere.
- Inclusivity: Juniors get quick wins; seniors get to practice explanation skills and showcase thought processes.

If you’re coaching or leading a team, AoC is a low‑friction way to create a rhythm of learning and positive energy in December.

## A craftsmanship take: treat AoC as docs‑as‑code

I keep each puzzle in a small, testable project structure. The point is not just to “get the stars,” but to capture a reproducible path to the answer.

Suggested micro-structure per day:

```
dayXX/
  input.txt            # puzzle input
  sample.txt           # samples from the statement
  solve_part1.{ext}    # returns the numeric/string answer
  solve_part2.{ext}
  test/                # unit tests or a tiny harness with expected I/O
  README.md            # notes, complexity, pitfalls
```

And a tiny CLI harness that mirrors real production expectations: deterministic, idempotent, with explicit inputs and outputs.

```bash
# Example (language‑agnostic idea)
./solve --day 05 --part 1 --input input.txt
./solve --day 05 --part 2 --input input.txt
```

Even for puzzles, I prefer a quick test around the parsing. Bugs usually hide there, not in the algorithm:

```python
# test_day05.py
from day05 import parse, part1

def test_parse_sample():
    data = parse(open('sample.txt').read())
    assert data.start == 'JNZ' and len(data.instructions) == 120

def test_part1_sample():
    data = parse(open('sample.txt').read())
    assert part1(data) == 157
```

## Sharpening core algorithms (with examples)

AoC deliberately exercises a wide range of patterns. A few that recur and are worth revisiting:

- Parsing: tokenization, state machines, small DSLs. Write a `parse()` that returns a strongly typed structure.
- Search: BFS/DFS on grids/graphs; bidirectional BFS for performance; Dijkstra/A* when weighted.
- Dynamic programming: bottom‑up tables for knapsack‑like constraints; memoized recursion for combinatorics.
- Geometry on grids: flood fill, line sweeps, visibility, connected components.
- Simulation: discrete time steps with invariants, careful with O(n²) traps.
- Data structures: heaps/priority queues, deque for sliding windows, disjoint sets for connectivity.

Keeping these fresh lowers the cognitive cost when a production incident or feature requires the same techniques.

## How we use AoC to motivate peers

Here’s a minimal, copy‑paste‑able pattern that worked for me:

1) Create a private leader board on adventofcode.com and post the join code in chat.
2) Pick a timebox (e.g., 45 minutes at lunch) twice a week. People may also solve asynchronously.
3) Ask volunteers to show a 5‑minute walkthrough of their solution. Emphasize trade‑offs, not cleverness.
4) Keep it tools‑light: no mandatory languages, no boilerplate repos. The goal is learning and camaraderie.
5) Celebrate small wins. A single star or a neat refactor is enough to share.

## From AoC to an internal kata platform

AoC inspired me to build a tiny platform to run internal events as a software craftsman coach—especially to facilitate an Extreme Startup Kata. In this format, teams register their own server, which must answer a stream of real‑time requests with progressively more challenging and varied questions. The idea is a small coordinating server that:

- Serves a sequence of problems with increasing difficulty.
- Accepts teams’ submissions via HTTP.
- Scores in real time and displays a live leaderboard on a simple web page.
- Makes each challenge deterministic and replayable for learning.

A sketch of the API surface:

```http
GET  /api/challenges            # list available challenges
GET  /api/challenges/{id}       # fetch statement, samples, constraints
POST /api/teams/register        # body: { team, callbackUrl } for team servers
POST /api/submissions           # body: { team, challengeId, answer }
GET  /api/leaderboard           # current ranking with timestamps
```

And a minimal request/response example:

```bash
curl -X POST https://events.local/api/submissions \
  -H 'Content-Type: application/json' \
  -d '{"team":"caring-coders","challengeId":"day05-p2","answer":"157_892"}'

# →
{"status":"accepted","points":3,"rank":2,"nextHintAt":"00:05:00"}
```

Implementation notes:

- Keep problems as data (YAML/JSON) so you can iterate without redeploying the server.
- Use a stateless scoring function per challenge; persist only submissions and scores.
- Provide a “replay” mode that re-runs all submissions to validate scoring changes.
- For workshops, expose a read‑only WebSocket or SSE endpoint for real‑time leaderboard updates.

This internal platform borrows AoC’s strengths—tight feedback, incremental difficulty, small surfaces—and adapts them to local team goals.

## Getting started (checklist)

- Join AoC and pick your language for this year.
- Choose or build minimal scaffolding in your preferred language. Do not copy mine—it’s highly personal and Java‑oriented. Use my repo only to skim ideas: https://github.com/caring-coder/adventofcode
- Set up a micro-harness to parse input and run parts 1/2 quickly.
- Schedule two short group sessions per week in December.
- If coaching, prepare 3–5 Extreme Startup challenges on your internal server to keep the momentum after AoC.

Community: if you want to discuss approaches or compare heuristics, join existing forums with many active participants:

- Reddit: https://www.reddit.com/r/adventofcode/
- Hacker News: search for the daily “Advent of Code Day N” threads on https://news.ycombinator.com/

## Closing

Advent of Code works because it turns deliberate practice into a tiny, rewarding habit. It keeps our fundamentals sharp and creates space for peers to learn from each other. For me, it also triggered a durable asset: a small internal events server that powers Extreme Startup‑style katas all year long.
