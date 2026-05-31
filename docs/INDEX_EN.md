# M.A.R.I.A. — navigation hub

> The entry point into the M.A.R.I.A. ecosystem documentation. This
> is a **map of meaning and materials**, not a SPEC and not a
> roadmap.
>
> Russian mirror: [`INDEX_RU.md`](INDEX_RU.md).

---

## Where to start

| If you are... | Go to |
|---|---|
| First time here, want a short “what is this” | [`FAQ_EN.md`](FAQ_EN.md) |
| Want to understand **why** the project exists | [`PROJECT_PHILOSOPHY_EN.md`](PROJECT_PHILOSOPHY_EN.md) |
| Want to see **how philosophy became code** | [`CONCEPTS_EN.md`](CONCEPTS_EN.md) |
| Want the technical roadmap of the current line | [`REBIRTH_1_1_NEW_FOUNDATION_EN.md`](REBIRTH_1_1_NEW_FOUNDATION_EN.md) |
| Want the author’s personal devlog | [`MARIA_DEVLOG_EN.md`](MARIA_DEVLOG_EN.md) |
| Want the archive of how we got here | `archive/` below |
| Want to spin the architectural showcase locally | [`FAQ_EN.md` → “Can I download it and run it locally?”](FAQ_EN.md#can-i-download-it-and-run-it-locally) |
| Want to dive into the technical SPECs | [`M.A.R.I.A.-Core`](https://github.com/HontoUKI/M.A.R.I.A.-Core) |

---

## Reading paths

### Path: “First time here, 10 minutes”

1. [`README.md`](../README.md) — what M.A.R.I.A. is.
2. [`FAQ_EN.md`](FAQ_EN.md) — answers to the main questions.
3. One concept of your choice from
   [`CONCEPTS_EN.md`](CONCEPTS_EN.md) — for example § 1 (actor
   model) or § 3 (perception-first).

### Path: “I want to understand the idea”

1. [`PROJECT_PHILOSOPHY_EN.md`](PROJECT_PHILOSOPHY_EN.md) — full
   philosophy, Rebirth narrative, public/private boundary, author
   afterword.
2. [`CONCEPTS_EN.md`](CONCEPTS_EN.md) — 5 concepts with examples:
   how philosophy becomes architecture.
3. [`REBIRTH_1_1_NEW_FOUNDATION_EN.md`](REBIRTH_1_1_NEW_FOUNDATION_EN.md)
   — narrative of the current line.

### Path: “I want to understand the architecture”

1. [`CONCEPTS_EN.md`](CONCEPTS_EN.md) — concepts as the entry
   point.
2. [`REBIRTH_1_1_NEW_FOUNDATION_EN.md`](REBIRTH_1_1_NEW_FOUNDATION_EN.md)
   — overall shape of Core 1.1.x.
3. Technical roadmap →
   [`M.A.R.I.A.-Core/docs/roadmap/REBIRTH_1_1_NEW_FOUNDATION.md`](https://github.com/HontoUKI/M.A.R.I.A.-Core/blob/main/docs/roadmap/REBIRTH_1_1_NEW_FOUNDATION.md).
4. Active package SPECs → `M.A.R.I.A.-Core/core/*/__SPEC__.md`.

### Path: “I want the history”

1. [`MARIA_DEVLOG_EN.md`](MARIA_DEVLOG_EN.md) — author devlog.
2. [`archive/CORE_DEV_LOG_NEW_HORIZON.md`](archive/CORE_DEV_LOG_NEW_HORIZON.md)
   — journal of the 0.x era.
3. [`archive/CORE_DEV_LOG_REBIRTH_1_0.md`](archive/CORE_DEV_LOG_REBIRTH_1_0.md)
   — journal of the Rebirth 1.0.x line.

---

## Map of documents

### Philosophy and direction

| Document | What is inside |
|---|---|
| [`PROJECT_PHILOSOPHY_EN.md`](PROJECT_PHILOSOPHY_EN.md) | Rebirth narrative, actor-model framing, public/private boundary, author afterword, “on the cost of development”. |
| [`REBIRTH_1_1_NEW_FOUNDATION_EN.md`](REBIRTH_1_1_NEW_FOUNDATION_EN.md) | Ecosystem narrative of the current Core line. Why a subtractive rewrite, what Core owns, what belongs to external clients. |
| [`MARIA_DEVLOG_EN.md`](MARIA_DEVLOG_EN.md) | Author chronicle of project evolution. |

### Applied documents (new)

| Document | What is inside |
|---|---|
| [`CONCEPTS_EN.md`](CONCEPTS_EN.md) | 5 concepts traced from philosophy → architecture → example. The main place to see how ideas become code. |
| [`FAQ_EN.md`](FAQ_EN.md) | Short answers to the 10 most frequent questions. |

### Archive (closed eras)

| Document | What is inside |
|---|---|
| [`archive/CORE_DEV_LOG_NEW_HORIZON.md`](archive/CORE_DEV_LOG_NEW_HORIZON.md) | DEV_LOG for the `0.1.x` – `0.20.4.x` era (New Horizon). |
| [`archive/CORE_DEV_LOG_REBIRTH_1_0.md`](archive/CORE_DEV_LOG_REBIRTH_1_0.md) | DEV_LOG for the Rebirth 1.0.x line (foundation modules). |

---

## External resources

### Ecosystem repositories

| Repo | Purpose | Status |
|---|---|---|
| [`M.A.R.I.A.-Core`](https://github.com/HontoUKI/M.A.R.I.A.-Core) | Public sanitized snapshot of the Core runtime (Apache-2.0). | Frozen at `public-final` (1.1.3repair) — no longer updated. |
| [`M.A.R.I.A.-WebUI`](https://github.com/HontoUKI/M.A.R.I.A.-WebUI) | Debug/developer cockpit. | Public; awaiting migration to the new FastAPI contract. |
| [`M.A.R.I.A.-Voice`](https://github.com/HontoUKI/M.A.R.I.A.-Voice) | External local voice runtime: STT, TTS, VAD, playback and diagnostics. | Public; active bootstrap. Current local TTS baseline: Silero Baya 48 kHz + WAV polish. |

Future ecosystem modules (planned as separate clients): CV,
**Presence-Shell** (Tauri-based desktop shell — avatar rendering
through Live2D / sprite inside its web layer plus window / audio
bridge; replaces the earlier separate Avatar + Unity plans),
Discord — all outside Core, through the
[`contracts/`](https://github.com/HontoUKI/M.A.R.I.A.-Core/tree/main/contracts)
typed envelopes.

### What lives where

```
This repo (M.A.R.I.A.)         — philosophy, ecosystem map, public direction,
                                 history archive, narrative docs.
M.A.R.I.A.-Core                — runtime, API, FastAPI, contracts, memory,
                                 state, perception, tests, setup,
                                 Core-specific Project_state and CHANGE_LOG.
M.A.R.I.A.-WebUI               — debug cockpit (separate session).
M.A.R.I.A.-Voice               — external voice runtime: STT, TTS, VAD,
                                 playback, local diagnostics.
```

---

## Want to contribute?

The public Core mirror is frozen and does not accept PRs. The
M.A.R.I.A. ecosystem hub (this repo) accepts PRs on docs /
philosophy / narrative if they fit the author direction; please
open an issue before a PR.

Active character development is private by design. This is an
authored project, not an open community.

---

## Concept map at a glance

```
Philosophy                       Architectural decision              Example
─────────────────────────────   ──────────────────────────────      ───────────────────────────
"LLM plays Maria, is not her"   persona dir + Director +            AFFORDANCE_PERSONA_WEIGHTS
                                affordance choice                   + recency rotation
"Core is Maria"                 FastAPI shell + contracts/          SceneSnapshot POST contract
                                + external clients                  + chosen_affordance out
"Reacts to the feeling"         FeelingFrame + StateDeltaProposal   reason-coded bounded delta
"Worthy memory is protected"    MemoryCandidate + RetentionPolicy   routing table + drop-honor
"Relationships ≠ authority"     creator-context block + forbidden   0 servile / 0 guilt / 0 leak
                                literals tripwire                   scenario invariants
```

Full text in [`CONCEPTS_EN.md`](CONCEPTS_EN.md).
