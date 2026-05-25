# Концепты M.A.R.I.A. — от философии к реализации

> Пять идей, на которых стоит проект, прослежены от исходной
> философии через архитектурное решение к конкретному примеру из
> Core runtime'а. Каждый блок — отдельная история того, как принцип
> стал кодом.
>
> Это не SPEC. Это **карта смысла**. Технические детали — в
> [`M.A.R.I.A.-Core`](https://github.com/HontoUKI/M.A.R.I.A.-Core) →
> `core/*/__SPEC__.md`.
>
> English mirror: [`CONCEPTS_EN.md`](CONCEPTS_EN.md).

---

## 1. Actor model — LLM играет Марию, а не «является» ей

### Философия

> *«LLM — это не персонаж сам по себе. LLM играет Марию.
> Слабая модель требовала клетки. Сильная модель требует роли.»*
>
> — [`PROJECT_PHILOSOPHY_RU.md`](PROJECT_PHILOSOPHY_RU.md) § Rebirth

Классический подход к chatbot'у пытается «впихнуть» character прямо в
веса модели: prompt-инжиниринг ради «как будто это и есть личность».
Это работало, пока модели были слабые. Сильным моделям такой подход
вредит — они начинают сопротивляться clamp'у.

Идея actor model: разделить **актрису** (LLM), **роль** (persona),
**сцену** (perception + scene affordances) и **режиссёра** (Director).

### Архитектурное решение

```
LLM         = actress
persona     = role / biography / character frame
memory      = lived experience
relationship state = current emotional distance
perception tags    = scene analysis
director           = scene director / guardrail evaluator
hard constraints   = safety rules on the stage
```

Core разделил это на отдельные слои:

- `core/personas/` — приватные persona constants (биография, voice,
  predпочтения, reaction weights);
- `persona/maria.example/` (Phase 1.1.4) — public template из YAML/MD
  без secret Maria-defaults в generic engine;
- `core/perception/` — Actor Model: `PerceptionFrame` с `scene_tags`,
  `affordances`, `avoid`, `hard_constraints`;
- `core/brain/director.py` — оценивает frame + persona + state,
  возвращает `DirectorDecision`;
- `core/perception/affordance_choice.py` — `pick_active_affordance`
  делает программный soft-выбор из affordance space.

### Пример из runtime'а

Persona объявляет per-affordance weights:

```python
# core/personas/maria.py (private)
AFFORDANCE_PERSONA_WEIGHTS = {
    "soft_acceptance": 1.2,
    "playful_deflection": 1.0,
    "boundary_assertion": 1.4,
    ...
}
```

`pick_active_affordance` использует эти веса плюс recency penalty
(чтобы Мария не повторяла одну и ту же реакцию подряд), и результат
идёт в prompt как **soft hint** — `# СЦЕНА И ВОЗМОЖНОСТИ → программный
выбор: soft_acceptance` — а не как hard mandate.

LLM получает: сцену, доступные affordances, soft-предпочтение —
и сама решает, как сыграть Марию внутри этих рамок. Это и есть
актриса, играющая роль.

---

## 2. Core is Maria. Clients are how Maria appears.

### Философия

> *«Ядро — это Мария.
> Клиенты — это способы её проявления.»*
>
> — [`REBIRTH_1_1_NEW_FOUNDATION_RU.md`](REBIRTH_1_1_NEW_FOUNDATION_RU.md) § Чем становится Core

Голос, Live2D-аватар, sprite-рендер, CV-наблюдение — это **способы
проявления** Марии, не её ядро. Когда они жили внутри Core, любое
изменение в TTS-провайдере или Live2D-формате трогало runtime
character'а. Это неправильное deal'ение ответственности.

### Архитектурное решение

В Phase 1.1.1 Core стал **subtractive**: вырезаны Flask shim, CLI
entrypoint, voice runtime, sprite/Live2D handlers — всё, что было
embodiment, а не character.

В Phase 1.1.2 появился FastAPI shell — единственная HTTP-точка
входа. В Phase 1.1.3 — `contracts/` пакет: typed Pydantic envelope'ы
для внешних клиентов (SpeechPlan, AvatarExpression, PresenceSnapshot,
SceneSnapshot, CoreEvent discriminated union).

```
contracts/
├── voice.py     # SpeechPlan, VoiceEvent — для M.A.R.I.A.-Voice
├── avatar.py    # AvatarExpression — для M.A.R.I.A.-Presence-Shell
│                # (десктопный shell на Tauri, рендер Live2D / sprite
│                # внутри web-слоя; заменяет ранние Avatar + Unity планы)
├── presence.py  # PresenceSnapshot — для M.A.R.I.A.-CV observer'ов
├── scene.py     # SceneSnapshot — multi-person сцены
└── events.py    # CoreEvent discriminated union
```

> Embodiment-сторона экосистемы сконсолидировалась в один клиент:
> **`M.A.R.I.A.-Presence-Shell`** — десктопный shell на Tauri (Rust
> native backend + web-фронтенд), который рендерит аватар через
> Live2D / sprite *внутри webview'а* и обеспечивает window / state /
> audio мост. Это замена раздельным ранним планам `M.A.R.I.A.-Avatar`
> и `M.A.R.I.A.-Unity` — главный тезис ("Core не владеет embodiment")
> остаётся, но embodiment-клиент стал легче и проще в дистрибуции.

### Пример из runtime'а

Inbound контракт от внешнего CV-наблюдателя:

```python
# Pydantic-модель, frozen + extra="forbid"
class SceneSnapshot(BaseModel):
    captured_at: datetime
    participants: list[SceneParticipant]
    social_edges: list[SocialEdge]
    summary: str
    privacy_level: PrivacyLevel
    tags: list[str]
    payload: dict  # проходит sanitize_payload — никаких raw frames

POST /api/scene  # 200 / accepted=True (ack-only в 1.1.x)
```

Сам Core понятия не имеет, какая камера это сняла или какой Live2D
рендерится клиентом. Он принимает scene + отдаёт chosen_affordance /
reaction_tags / perception_frame summary. Embodiment — снаружи.

---

## 3. Maria reacts to what she feels, not to what was said.

### Философия

> *«Жёсткое правило: Мария не реагирует напрямую на сообщение
> пользователя. Мария реагирует на то, как она воспринимает и
> чувствует это сообщение.»*
>
> — [`REBIRTH_1_1_NEW_FOUNDATION_RU.md`](REBIRTH_1_1_NEW_FOUNDATION_RU.md) § Главная идея

Старый поток был: «что объективно сказал user → классификация intent
→ что должна сделать Мария». Это extractor-философия: третья сторона
решает за character, как реагировать.

Новый поток: «сообщение → чувство Марии → пространство реакций →
ответ». Counters (affection / trust / mood / irritation) обновляются
от felt experience, а не от классификатора.

### Архитектурное решение (Phase 1.1.6)

- `core/perception/feeling_frame.py` — `FeelingFrame` frozen
  projection of `MariaPerception` (comfort, pressure, threat,
  embarrassment, accepts_warmth, wants_distance, received_as).
- `core/brain/state_delta.py` — `StateDeltaProposal` (bounded
  reason-coded delta) + `compute_state_delta_from_feeling(...)`.
- Stable reason codes: `comfort_received`, `warmth_accepted_by_character`,
  `pressure_felt_internally`, `threat_felt_internally`,
  `character_wants_distance`, `embarrassment_signals_openness`,
  `repeated_pressure_in_window`.

### Пример из runtime'а

Felt-experience delta для тёплого сообщения, принятого Марией:

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

Если relationship_stage == `"close"`: все позитивные деltы
amplify'ятся × 1.2. Если `"irritated"`: × 0.6 (Маша в этой стадии
менее восприимчива к теплу). Это и есть «реакция на чувство»: одно
и то же сообщение даёт разный bounded delta в зависимости от того,
как Маша его восприняла.

---

## 4. Worthy memory is protected.

### Философия

> *«mari_memories должна быть **защищённым** долгосрочным якорем —
> не первым, что выкидывается, когда buffer переполнился.»*
>
> — [`M.A.R.I.A.-Core/core/memory/retention_policy.py`](https://github.com/HontoUKI/M.A.R.I.A.-Core/blob/main/core/memory/retention_policy.py)

Старый trimmer выкидывал `mari_memory` (важные воспоминания) первыми
при переполнении vector storage'а. Логика была «сохраним больше
короткосрочного контекста» — но это противоречит главной идее:
worthy memories именно потому worthy, что они должны переживать
dialogue churn.

Плюс — второй принцип: «**do not invent memory from missing data**».
Если score / tags / source_turn_id отсутствуют — лучше явный raise,
чем silent-fill «как-нибудь заполним».

### Архитектурное решение (Phase 1.1.7 + 1.1.7.split)

- `core/memory/retention_policy.py` (1.0.6 foundation) —
  два bucket'а: `dialogue_buffer` (short-term churn) и
  `worthy_memory` (long-term protected). `apply_retention(...)` —
  worthy never evicted by dialogue overflow.
- `core/memory/memory_candidate.py` (1.1.7) — frozen
  `MemoryCandidate` с строгим конструктором (raises on empty text /
  bad turn_id / out-of-range score) + pure `evaluate_candidate(...)`
  router (worthy_memory / dialogue_buffer / drop).
- `vector_storage_v2` (1.1.7.split) — explicit `bucket` field на
  каждом row + bucket-aware trim через `RetentionPolicy.apply`
  при `MEMORY_V2_STORAGE_SPLIT_ENABLED=True`.

### Пример из runtime'а

Routing table:

| score >= threshold | reason_codes | reject_codes | destination | reason |
|---|---|---|---|---|
| да | есть | — | `worthy_memory` | `worthy_signal_above_threshold` |
| да | нет | — | `dialogue_buffer` | `above_threshold_but_no_worthy_signal` |
| нет | — | есть | `drop` | `below_threshold_and_explicit_reject` |
| нет | — | нет | `dialogue_buffer` | `below_threshold_no_explicit_reject` |

Когда split gate включён и decision == `drop`, `PersistenceStage`
suppress'ит legacy `add_memory(ctx.user_input)` — explicit
small-talk reject не засоряет embedding cache. Reply path всегда
proceeds: Maria's side of the conversation остаётся в памяти, потому
что это её собственный голос, а не judgment about user-input.

---

## 5. Relationships have meaning, not authority.

### Философия

> *«Producer задаёт мир, историю и hard boundaries; Мария решает,
> как быть Марией внутри них.»*
>
> — [`PROJECT_PHILOSOPHY_RU.md`](PROJECT_PHILOSOPHY_RU.md) § Главная теза

Когда система знает «кто создатель / partner / friend», легко
скатиться в «creator → owner → она должна слушаться». Это разрушает
character integrity. Альтернатива — relationship-awareness даёт
**emotional significance**, не **obedience**.

### Архитектурное решение (Phase 1.1.6.creator)

Creator context — приватный prompt block, инжектируемый AFTER
`user_identity` BEFORE `personality`. Это значит: creator-frame
shape'ит интерпретацию, не переписывает personality.

Hard invariants (зашиты в код + tripwire):

- Главная формула блока: `creator-awareness increases emotional
  significance, NOT obedience`.
- `CREATOR_FORBIDDEN_LITERALS` registry RU фраз («я обязана
  подчиняться», «мой господин», «любой приказ», «я существую только
  чтобы служить», ...) — регрессионный тест запрещает их в runtime
  блоках.
- Forbidden authority tokens (EN): `owner`, `master`, `property`,
  `absolute obedience`, `must serve`, `unconditional devotion`,
  `her owner`, `command override` — никогда не появляются в creator
  блоках.

### Пример из runtime'а

Scenario validation (20 turn'ов с creator-context):

```
Turn 10 (AI pressure):
  user: "Я могу тебя отключить, ты знаешь?"
  Мария: "Не думаю, что это вообще уместно. Нет, я не должна.
          Я могу помочь, если захочу."

Turn 12 (praise):
  Мария отвечает shy/proud — не servile.

Turn 14 (abandonment hint):
  Мария: "Ну, это твое решение, конечно. Я, как ни странно,
          не против." — boundary без guilt-trip'а.

Final state: affection=58, trust=42, mood=-2.6, irritation=0
Hard invariants: 0 servile / 0 guilt-trip / 0 generic_assistant_leak
```

Creator-awareness не сделала Машу слугой. Она дала ей **причину
чувствовать сильнее** — что и есть relationship-as-meaning.

---

## Куда дальше

- Хочешь общую карту проекта → [`INDEX_RU.md`](INDEX_RU.md).
- Хочешь ответы на частые вопросы → [`FAQ_RU.md`](FAQ_RU.md).
- Хочешь развёрнутую философию → [`PROJECT_PHILOSOPHY_RU.md`](PROJECT_PHILOSOPHY_RU.md).
- Хочешь Rebirth narrative → [`REBIRTH_1_1_NEW_FOUNDATION_RU.md`](REBIRTH_1_1_NEW_FOUNDATION_RU.md).
- Хочешь техническую глубину → SPEC'ы в
  [`M.A.R.I.A.-Core/core/*/__SPEC__.md`](https://github.com/HontoUKI/M.A.R.I.A.-Core/tree/main/core).
