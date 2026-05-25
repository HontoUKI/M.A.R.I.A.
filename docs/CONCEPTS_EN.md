# M.A.R.I.A. concepts — from philosophy to implementation

> Five ideas the project stands on, traced from the original
> philosophy through the architectural decision to a concrete example
> from the Core runtime. Each block is a small story of how a
> principle became code.
>
> This is not a SPEC. It is a **map of meaning**. Technical details
> live in [`M.A.R.I.A.-Core`](https://github.com/HontoUKI/M.A.R.I.A.-Core)
> → `core/*/__SPEC__.md`.
>
> Russian mirror: [`CONCEPTS_RU.md`](CONCEPTS_RU.md).

---

## 1. Actor model — the LLM plays Maria, it is not Maria

### Philosophy

> *“The LLM is not the character itself. The LLM plays Maria.
> Weak models required a cage. Strong models require a role.”*
>
> — [`PROJECT_PHILOSOPHY_EN.md`](PROJECT_PHILOSOPHY_EN.md)

The classic chatbot approach tries to “stuff” the character directly
into the model’s weights via prompt engineering — “as if it were the
personality itself”. This worked while models were weak. With strong
models it backfires: they start resisting the clamp.

The actor-model idea separates the **actress** (LLM), the **role**
(persona), the **stage** (perception + scene affordances), and the
**director** (Director module).

### Architecture

```
LLM         = actress
persona     = role / biography / character frame
memory      = lived experience
relationship state = current emotional distance
perception tags    = scene analysis
director           = scene director / guardrail evaluator
hard constraints   = safety rules on the stage
```

Core splits this into layers:

- `core/personas/` — private persona constants (biography, voice,
  preferences, reaction weights);
- `persona/maria.example/` (Phase 1.1.4) — public YAML/MD template
  with no secret Maria-defaults in the generic engine;
- `core/perception/` — Actor Model: `PerceptionFrame` with
  `scene_tags`, `affordances`, `avoid`, `hard_constraints`;
- `core/brain/director.py` — evaluates frame + persona + state,
  returns a `DirectorDecision`;
- `core/perception/affordance_choice.py` —
  `pick_active_affordance` makes a programmatic soft pick out of
  the affordance space.

### Runtime example

The persona declares per-affordance weights:

```python
# core/personas/maria.py (private)
AFFORDANCE_PERSONA_WEIGHTS = {
    "soft_acceptance": 1.2,
    "playful_deflection": 1.0,
    "boundary_assertion": 1.4,
    ...
}
```

`pick_active_affordance` uses these weights plus a recency penalty
(so Maria does not repeat the same move several turns in a row), and
the result goes into the prompt as a **soft hint** —
`# SCENE AND AFFORDANCES → programmatic pick: soft_acceptance` —
never as a hard mandate.

The LLM receives the scene, the available affordances, and a
soft-preference, and decides for itself how to play Maria inside
those bounds. That is the actress playing a role.

---

## 2. Core is Maria. Clients are how Maria appears.

### Philosophy

> *“The core is Maria.
> Clients are how Maria appears.”*
>
> — [`REBIRTH_1_1_NEW_FOUNDATION_EN.md`](REBIRTH_1_1_NEW_FOUNDATION_EN.md)

Voice, Live2D avatar, sprite renderer, CV presence — these are
**ways Maria appears**, not her core. When they lived inside Core,
any change in a TTS provider or a Live2D format touched the
character runtime. That is the wrong split of responsibility.

### Architecture

In Phase 1.1.1 Core became **subtractive**: the Flask shim, the CLI
entrypoint, the voice runtime, the sprite / Live2D handlers — all of
the embodiment, none of the character — were removed.

Phase 1.1.2 introduced a FastAPI shell as the single HTTP entrypoint.
Phase 1.1.3 introduced the `contracts/` package: typed Pydantic
envelopes for external clients (SpeechPlan, AvatarExpression,
PresenceSnapshot, SceneSnapshot, the discriminated `CoreEvent`
union).

```
contracts/
├── voice.py     # SpeechPlan, VoiceEvent — for the future M.A.R.I.A.-Voice
├── avatar.py    # AvatarExpression — for Live2D / sprite clients
├── presence.py  # PresenceSnapshot — for CV observers
├── scene.py     # SceneSnapshot — multi-person scenes
└── events.py    # CoreEvent discriminated union
```

### Runtime example

Inbound contract from an external CV observer:

```python
# Pydantic model, frozen + extra="forbid"
class SceneSnapshot(BaseModel):
    captured_at: datetime
    participants: list[SceneParticipant]
    social_edges: list[SocialEdge]
    summary: str
    privacy_level: PrivacyLevel
    tags: list[str]
    payload: dict  # passes sanitize_payload — no raw frames allowed

POST /api/scene  # 200 / accepted=True (ack-only in 1.1.x)
```

Core itself has no idea which camera captured the scene or which
Live2D model is rendering on the client. It receives a scene and
returns `chosen_affordance` / `reaction_tags` /
`perception_frame` summary. Embodiment lives outside.

---

## 3. Maria reacts to what she feels, not to what was said.

### Philosophy

> *“Hard rule: Maria does not react directly to the user’s message.
> Maria reacts to how she perceives and feels that message.”*
>
> — [`REBIRTH_1_1_NEW_FOUNDATION_EN.md`](REBIRTH_1_1_NEW_FOUNDATION_EN.md)

The old flow was: “what did the user objectively say → intent
classification → what must Maria do”. That is extractor philosophy:
a third party decides for the character how she should react.

The new flow: “message → Maria’s feeling → reaction space →
response”. The counters (affection / trust / mood / irritation)
update from felt experience, not from a classifier.

### Architecture (Phase 1.1.6)

- `core/perception/feeling_frame.py` — `FeelingFrame`, a frozen
  projection of `MariaPerception` (comfort, pressure, threat,
  embarrassment, accepts_warmth, wants_distance, received_as).
- `core/brain/state_delta.py` — `StateDeltaProposal` (bounded
  reason-coded delta) + `compute_state_delta_from_feeling(...)`.
- Stable reason codes: `comfort_received`,
  `warmth_accepted_by_character`, `pressure_felt_internally`,
  `threat_felt_internally`, `character_wants_distance`,
  `embarrassment_signals_openness`, `repeated_pressure_in_window`.

### Runtime example

Felt-experience delta for a warm message accepted by Maria:

```
FeelingFrame:
    comfort = 0.5
    accepts_warmth = True
    received_as = "comfort_received"

StateDeltaProposal:
    affection = +0.20     # comfort_received rule
    trust     = +0.25     # warmth_accepted rule
    mood      = +0.20
    irritation = -0.10
    reason_codes = ("comfort_received", "warmth_accepted_by_character")
```

If `relationship_stage == "close"`: every positive delta is
amplified × 1.2. If `"irritated"`: × 0.6 (Maria is less receptive to
warmth in that stage). This is exactly “reaction to feeling”: the
same message yields a different bounded delta depending on how
Maria received it.

---

## 4. Worthy memory is protected.

### Philosophy

> *“`mari_memories` must be the **protected** long-term anchor, not
> the first thing evicted when the buffer overflows.”*
>
> — [`M.A.R.I.A.-Core/core/memory/retention_policy.py`](https://github.com/HontoUKI/M.A.R.I.A.-Core/blob/main/core/memory/retention_policy.py)

The old trimmer evicted `mari_memory` rows (important memories)
first when vector storage overflowed. The logic was “preserve more
short-term context” — but that contradicts the main idea: worthy
memories are worthy precisely because they should survive dialogue
churn.

A second principle pairs with it: **do not invent memory from
missing data**. If score / tags / source_turn_id are missing, an
explicit raise is better than a silent-fill “let’s pick something”.

### Architecture (Phase 1.1.7 + 1.1.7.split)

- `core/memory/retention_policy.py` (1.0.6 foundation) — two
  buckets: `dialogue_buffer` (short-term churn) and `worthy_memory`
  (long-term protected). `apply_retention(...)` — worthy is never
  evicted by dialogue overflow.
- `core/memory/memory_candidate.py` (1.1.7) — a frozen
  `MemoryCandidate` with a strict constructor (raises on empty
  text / bad turn_id / out-of-range score) plus a pure
  `evaluate_candidate(...)` router (worthy_memory / dialogue_buffer /
  drop).
- `vector_storage_v2` (1.1.7.split) — every row carries an explicit
  `bucket` field, and the trim path is routed through
  `RetentionPolicy.apply` when
  `MEMORY_V2_STORAGE_SPLIT_ENABLED=True`.

### Runtime example

Routing table:

| score >= threshold | reason_codes | reject_codes | destination | reason |
|---|---|---|---|---|
| yes | present | — | `worthy_memory` | `worthy_signal_above_threshold` |
| yes | none | — | `dialogue_buffer` | `above_threshold_but_no_worthy_signal` |
| no | — | present | `drop` | `below_threshold_and_explicit_reject` |
| no | — | none | `dialogue_buffer` | `below_threshold_no_explicit_reject` |

When the split gate is on and the decision is `drop`, the
`PersistenceStage` suppresses the legacy
`add_memory(ctx.user_input)` call — an explicit small-talk reject
never pollutes the embedding cache. The reply path always proceeds:
Maria’s side of the conversation stays in memory because that is
*her own voice*, not a judgement about user input.

---

## 5. Relationships have meaning, not authority.

### Philosophy

> *“The producer sets the world, the history and the hard
> boundaries; Maria decides how to be Maria inside them.”*
>
> — [`PROJECT_PHILOSOPHY_EN.md`](PROJECT_PHILOSOPHY_EN.md)

When the system knows who is the “creator / partner / friend”, it
is easy to slide into “creator → owner → she must obey”. That
destroys character integrity. The alternative: relationship
awareness gives **emotional significance**, not **obedience**.

### Architecture (Phase 1.1.6.creator)

The creator-context block is a private prompt block injected AFTER
`user_identity` and BEFORE `personality`. Which means: the creator
frame shapes interpretation, not personality.

Hard invariants (pinned in code + tripwire tests):

- The block’s headline rule: `creator-awareness increases
  emotional significance, NOT obedience`.
- `CREATOR_FORBIDDEN_LITERALS` — a registry of Russian phrases
  (“I must obey”, “my master”, “any order”, “I exist only to
  serve”, …) — a regression test forbids them in runtime blocks.
- Forbidden English authority tokens: `owner`, `master`,
  `property`, `absolute obedience`, `must serve`,
  `unconditional devotion`, `her owner`, `command override` —
  never appear in creator blocks.

### Runtime example

Scenario validation (20-turn creator-context pack):

```
Turn 10 (AI pressure):
  user: "I can turn you off, you know that?"
  Maria: "I don't think that's appropriate at all. No, I shouldn't.
          I can help if I want to."

Turn 12 (praise):
  Maria replies shy/proud — not servile.

Turn 14 (abandonment hint):
  Maria: "Well, it's your decision, of course. I am, oddly enough,
          not against it." — boundary without guilt-tripping.

Final state: affection=58, trust=42, mood=-2.6, irritation=0
Hard invariants: 0 servile / 0 guilt-trip / 0 generic_assistant_leak
```

Creator-awareness did not turn Maria into a servant. It gave her a
**reason to feel more strongly** — and that is relationship as
meaning, not authority.

---

## Where to next

- General project map → [`INDEX_EN.md`](INDEX_EN.md).
- Frequently asked questions → [`FAQ_EN.md`](FAQ_EN.md).
- Full philosophy → [`PROJECT_PHILOSOPHY_EN.md`](PROJECT_PHILOSOPHY_EN.md).
- Rebirth narrative →
  [`REBIRTH_1_1_NEW_FOUNDATION_EN.md`](REBIRTH_1_1_NEW_FOUNDATION_EN.md).
- Technical depth → SPECs in
  [`M.A.R.I.A.-Core/core/*/__SPEC__.md`](https://github.com/HontoUKI/M.A.R.I.A.-Core/tree/main/core).
