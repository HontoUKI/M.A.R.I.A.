# Rebirth 1.1.x — New Foundation (Ecosystem narrative)

> Ecosystem-level direction note for the **M.A.R.I.A.** project.
> The technical Core roadmap lives in
> [`M.A.R.I.A.-Core/docs/roadmap/REBIRTH_1_1_NEW_FOUNDATION.md`](https://github.com/HontoUKI/M.A.R.I.A.-Core/blob/main/docs/roadmap/REBIRTH_1_1_NEW_FOUNDATION.md).
> This document is the philosophical / direction announcement, not
> implementation.

## Why a New Foundation

The `0.x` era and Rebirth `1.0.x` proved that the local AI character
runtime works. It has memory, relationship state, opinions,
perception improvements, an actor model, scene affordances,
deterministic offline cognition, voice runtime experiments, a debug
WebUI and a public showcase.

The result is more than enough to prove that the idea is real.

It is also more than enough to see, in retrospect, that the
foundation accumulated historical weight: an old extractor
philosophy, hidden-thought legacy, fallback-driven behavior, a
god-file `maria.py`, voice / Live2D / sprite / CV runtime sitting
inside or next to Core, Flask compatibility adapters, and too many
unrelated mechanics living in one project.

**Rebirth 1.1.x** is the moment we stop polishing the old foundation
and rebuild it.

## Core idea

Old flow:

```
What did the user say objectively?
  → What intent / emotion classification?
  → What should Maria do?
```

New flow:

```
What could Maria feel after reading this?
  → What reaction space becomes available to her?
  → How does she answer as Maria?
```

Short formula:

```
Message → Maria's feeling → reaction space → response.
```

Hard rule:

```
Maria does not react directly to the user's message.
Maria reacts to how she perceives and feels the message.
```

## What `M.A.R.I.A.-Core` becomes

```
Core is Maria.
Clients are how Maria appears.
```

`M.A.R.I.A.-Core` becomes a **single-user FastAPI runtime** with:

- perception-first architecture;
- memory and relationship state;
- persona as a directory of documents (no more god-file);
- contracts and events as the outward boundary;
- no Voice / Live2D / Sprite / CV implementations inside Core.

External embodiment modules become separate ecosystem projects that
talk to Core through HTTP / events:

- `M.A.R.I.A.-Voice` (TTS / STT / VAD / playback);
- `M.A.R.I.A.-Avatar` (Live2D / sprite renderer / animation);
- `M.A.R.I.A.-CV` (camera / screen observers feeding presence
  snapshots);
- `M.A.R.I.A.-Unity` (full embodiment client).

These remain author-driven and intentionally decoupled — not
abandoned. They are out of Core because they should be out of Core.

## What stays single-user

Core is **single-user by design**. It owns:

```
one Maria
one primary user
one continuous local relationship timeline
one private character runtime
one private memory system
```

Scenes and other people are handled as **context** — usually provided
by external clients via scene snapshots — not as a multi-tenant
graph platform.

## What `M.A.R.I.A.` ecosystem hub owns

The ecosystem repository (this one) keeps:

- philosophy;
- ecosystem map;
- public direction;
- archived history (old DEV_LOGs from the New Horizon and Rebirth
  1.0.x eras);
- public / private boundary contract.

`M.A.R.I.A.-Core` keeps only technical Core documentation: runtime,
API, FastAPI, contracts, memory, state, perception, tests, setup,
Core-specific Project_state and CHANGE_LOG.

## Foundation lessons we keep

Rebirth 1.0.x ended with several foundation modules that survive
into the new architecture:

- typed prompt fragment / recipe registry;
- character-aware capability help modes;
- typed opinion schema v2 (leading-belief never becomes Maria
  preference);
- memory retention policy (worthy memories protected from dialogue
  churn);
- programmatic affordance choice (soft-only by construction).

These were intentionally "foundation-only" in 1.0.x — declared and
tested, but not yet wired. In 1.1.x the new architecture finally
consumes them.

## What this is not

Rebirth 1.1.x is **not** a rewrite from frustration.

It is a rewrite from experience.

```
We are not starting from zero.
We are starting from experience.
```

The old line is closed under the tag `1.0.x-closed`. The history,
the deleted modules, the early experiments — all of it stays
accessible through that tag. The new branch is allowed to be
subtractive, to delete code, to remove modules and to refuse to
carry old assumptions. The history is safe; the future has room.

## Reading order

1. This document.
2. `M.A.R.I.A.-Core/docs/roadmap/REBIRTH_1_1_NEW_FOUNDATION.md` —
   technical roadmap with phases, contracts and validation gates.
3. `M.A.R.I.A.-Core/Project_state.md` — current state and cleanup
   candidates.
4. `M.A.R.I.A.-Core/CHANGE_LOG.md` — active stage journal.

Russian mirror of this document:
[`REBIRTH_1_1_NEW_FOUNDATION_RU.md`](REBIRTH_1_1_NEW_FOUNDATION_RU.md).
