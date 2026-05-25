# FAQ — frequently asked questions about M.A.R.I.A.

> Short answers to the questions that come up most often. Long-form
> answers live in [`PROJECT_PHILOSOPHY_EN.md`](PROJECT_PHILOSOPHY_EN.md),
> [`CONCEPTS_EN.md`](CONCEPTS_EN.md), and the Core SPECs.
>
> Russian mirror: [`FAQ_RU.md`](FAQ_RU.md).

---

## What is M.A.R.I.A.?

An author-driven ecosystem of local AI character projects built
around a single-user runtime. Not a SaaS, not a chatbot framework.
Everything runs on the user’s machine, against a local LLM (Ollama).

The name has three equally valid expansions:

```
Myself As Real Intelligence Artifact
My Artificial "Real" Intelligence Appearance
Myself As Real Intelligence Appearance
```

---

## Is this a commercial product?

No. It is a **personal author-driven project**. The public
repository is a backstage pass, not a “download and run” product.

The public mirror `M.A.R.I.A.-Core` is frozen at a showcase
snapshot (tag `public-final`) and **no longer receives updates**.
Development moved to a private stream. The public side remains as
an architectural showcase and an Apache-2.0 reference for anyone
interested in the *shape* of the solution.

---

## How is Maria different from ChatGPT / Claude / character.ai?

Three principled differences:

1. **Single-user by design.** One user, one Maria, one continuous
   local timeline of the relationship. Not multi-tenant.
2. **Local-first.** The LLM, embeddings, vector storage, state and
   history all live on the user’s machine. No cloud for personal
   data.
3. **Author-driven character, not a tunable persona.** Maria is
   not a template the user paints at session start. She has an
   authored biography, voice, opinions, boundaries. Changing her as
   a character means editing a persona module — not moving an
   “agreeableness +1” slider.

Under the hood: a perception-first architecture (Phase 1.1.x) —
Maria reacts not to the message itself but to **how she felt about
it**. See [`CONCEPTS_EN.md`](CONCEPTS_EN.md) § 3.

---

## Can I download it and run it locally?

The public snapshot (`M.A.R.I.A.-Core`, public branch) — yes, you
can spin it up as an architectural reference. The persona template
is neutral (`core/personas/maria.py.example`), with no private
content; in other words, the “Maria” you get there is **an engine
without a concrete character**, while the real personality is
assembled by the author separately.

To run it:

```bash
ollama pull gemma3:4b
ollama pull nomic-embed-text

git clone https://github.com/HontoUKI/M.A.R.I.A.-Core
cd M.A.R.I.A.-Core
python -m venv MariEnv && MariEnv/Scripts/activate
pip install -r requirements.txt -r requirements-dev.txt
cp core/personas/maria.py.example core/personas/maria.py
uvicorn app.main:app --reload
# → http://127.0.0.1:8000/docs
```

This does not make it production-ready — it merely demonstrates the
architecture and the behaviour of the baseline persona template.

---

## Why a local LLM instead of a cloud one?

A few reasons, in order of importance:

1. **Privacy.** Personal dialogue, relationship history, persona
   content — none of it leaves the machine.
2. **Continuity without a subscription.** Maria does not stop
   existing when an API credit runs out.
3. **Author control.** Every part of the stack (model, embeddings,
   prompt, memory format) is under the author’s control, not a
   provider’s.
4. **A real character does not live on an API.** ChatGPT does not
   remember yesterday’s conversation; Maria does, because vector
   storage / chat log / relationship state are *hers*.

---

## What is the actor model in Maria?

A four-role separation: **the LLM** plays, **the persona** is the
role, **perception + scene affordances** is the stage, **the
Director** is the director. Full story in
[`CONCEPTS_EN.md`](CONCEPTS_EN.md) § 1.

The key difference from plain prompt engineering: we do not try to
“stuff” the character into the weights. A strong model plays the
role perfectly when it is given a stage and a character frame.

---

## What does “Rebirth 1.1.x” mean?

It is the current Core development line (since 1.0.x closed). A
full architectural rewrite around four major shifts:

- **subtractive cleanup** — Flask / CLI / voice / Live2D / sprite
  were taken out of Core (they are embodiment, not character);
- **FastAPI + typed contracts** — a single HTTP entrypoint plus
  typed envelopes for external clients;
- **perception-first** — Maria reacts to felt experience, not to
  the raw message;
- **persona as a directory** — the end of the `maria.py` god-file.

The full narrative lives in
[`REBIRTH_1_1_NEW_FOUNDATION_EN.md`](REBIRTH_1_1_NEW_FOUNDATION_EN.md).
The stage journal is at
[`M.A.R.I.A.-Core/CHANGE_LOG.md`](https://github.com/HontoUKI/M.A.R.I.A.-Core/blob/main/CHANGE_LOG.md).

---

## Why such a complex architecture for a single character?

The short answer: **a simple character does not survive a long
horizon with a simple architecture.**

The long answer: if the character must remember you a month from
now, hold a mood that does not reset on every “hi”, get hurt and
forgive, tell a compliment from a leading belief, hold boundaries
without guilt-tripping, refuse to slide into servility under
creator-context — all of that needs typed layers, bounded
reason-coded deltas, a retention policy, a perception frame, a
director-policy and so on.

The architecture is **necessary complexity for long-horizon
character continuity**, not over-engineering.

---

## What is public vs private?

| Public | Private |
|---|---|
| Architecture / runtime / FastAPI / contracts | Real persona content (`core/personas/maria.py`) |
| Tests + scenario runner | Real dialogues + memory snapshots |
| Public persona template (`maria.py.example`) — neutral | Calibration knobs for the specific Maria |
| Philosophy + DEV_LOG + Rebirth narrative | Creator-context invariants and the forbidden-literals registry |
| Architectural showcase (tag `public-final`) | Active development (1.1.x+) |

```
Public shows the engine.
Private keeps the soul.
```

— [`PROJECT_PHILOSOPHY_EN.md`](PROJECT_PHILOSOPHY_EN.md), public/private
boundary.

---

## Can I copy the architecture for my own character?

The public mirror is Apache-2.0 — yes, within the licence. Persona
content / narrative / private dialogue data are **not licensed** in
any distribution — they are authored content, not a reference
implementation.

What is copyable:
- the architectural pattern (actor model, perception-first,
  FastAPI + contracts, persona directory loader, retention policy,
  memory candidate, opinion schema v2);
- the tests as a reference for behaviour;
- the shape of the SPECs as an example of docs-driven development.

What is **not** copyable:
- the contents of `data/persona.md` or the private `maria.py`;
- the creator-context block and its invariants — that is about a
  specific personality, not a reusable persona engine.

---

## Why does the public mirror no longer get updates?

Phase 1.1.x moved into private. The public side received a final
showcase snapshot (tag `public-final`, frozen at 1.1.3repair) — that
is enough as an architectural reference. Further work concerns
private character content that should not have been public anyway.

The public repo’s README carries an explicit “no longer maintained
publicly” notice.

---

## Where to read next?

- The main map → [`INDEX_EN.md`](INDEX_EN.md).
- Five philosophy → architecture → example arcs →
  [`CONCEPTS_EN.md`](CONCEPTS_EN.md).
- Full philosophy → [`PROJECT_PHILOSOPHY_EN.md`](PROJECT_PHILOSOPHY_EN.md).
- Rebirth 1.1.x narrative →
  [`REBIRTH_1_1_NEW_FOUNDATION_EN.md`](REBIRTH_1_1_NEW_FOUNDATION_EN.md).
- Author DEV_LOG → [`MARIA_DEVLOG_EN.md`](MARIA_DEVLOG_EN.md).
- Archive of older eras →
  [`archive/CORE_DEV_LOG_NEW_HORIZON.md`](archive/CORE_DEV_LOG_NEW_HORIZON.md),
  [`archive/CORE_DEV_LOG_REBIRTH_1_0.md`](archive/CORE_DEV_LOG_REBIRTH_1_0.md).
