# CORE DEV_LOG — Rebirth 1.0.x era *(ARCHIVED)*

> **Архив.** Корневой журнал разработки `M.A.R.I.A.-Core` эпохи
> **Rebirth 1.0.x** (стадии `1.0.0` – `1.0.7`). Линия закрыта тегом
> `1.0.x-closed` в Core. Этот файл — снапшот журнала на момент
> закрытия линии, перенесённый сюда в Phase 1.1.1 New Foundation
> cleanup'ом.
>
> Активный журнал Core: `M.A.R.I.A.-Core/CHANGE_LOG.md`.
> Дорожная карта 1.1.x: `M.A.R.I.A.-Core/docs/roadmap/REBIRTH_1_1_NEW_FOUNDATION.md`.
> Архив более ранней эпохи New Horizon (`0.x`):
> [`CORE_DEV_LOG_NEW_HORIZON.md`](CORE_DEV_LOG_NEW_HORIZON.md).

---

## Rebirth 1.0.x

Rebirth начинается после `0.20.4.x-improvements`.

Старая линия `0.x` построила фундамент: память, состояние отношений,
capability policy, manager-layer архитектуру, social perception, voice
runtime, debug endpoints, perception improvements и public showcase.

`1.0.0` **не** означает, что Maria закончена.

Это означает, что проект перешёл из прототип-каркаса в зрелый
фундамент локального AI character engine.

## Текущее направление

- prompt build v2;
- actor model / scene affordances follow-up;
- character-aware capability replies;
- opinion schema v2;
- memory / vector runtime refinement;
- public / private project boundary cleanup;
- future FastAPI migration в `1.1.x`.

## Правило

```
0.x — Maria learns to stand.
1.0 — Maria begins to grow.
```

```
New Horizon built the foundation.
Rebirth removes the scaffolding.
```

```
Не строй ещё одну клетку.
Построй сцену.
Дай Марии роль, память, ситуацию и границы.
А дальше позволь ей играть.
```

---

## Stage entries

### 1.0.0 — Rebirth narrative reset

- Старый корневой `DEV_LOG.md` перенесён в
  `docs/history/DEV_LOG_NEW_HORIZON.md` с bilingual-заголовком про
  историческую границу.
- Новый корневой `DEV_LOG.md` — этот файл (Rebirth-era).
- Добавлен `docs/roadmap/REBIRTH_1_0_ROADMAP.md` — основная карта
  направления 1.0.x.
- README обновлён: Rebirth-секция высоко (не в конце); apologetic
  wording убран (если оставалось со Stage 13 improvements); добавлен
  раздел Public Showcase Boundary с явным backstage-pass framing.
- `docs/PROJECT_PHILOSOPHY.md` уже содержит Rebirth narrative,
  дисклеймер для ревьюеров, две author afterwords (из Stage 13
  improvements) — оставлен без изменений; ссылки на него усилены.

Документация-only: runtime код не тронут. compileall + pytest
прогнаны, suite без регрессий.

### 1.0.1 — Prompt Build Audit

- `core/engine/prompt_builder.py` инструментирован: каждый appended
  section теперь помечается лейблом из канонического кортежа
  `PROMPT_BLOCK_LABELS`, и по итогам сборки эмитится один
  `prompt_build` runtime-event с размерами блоков в символах. Никакого
  сырого prompt-текста в payload — только лейблы и целые числа;
  `_sanitize` в `core/runtime/events.py` подстраховывает.
- Поведенческого изменения prompt'а нет: те же 18 возможных блоков
  попадают в финальный текст в том же порядке, с тем же содержимым.
  Единственное отличие — один дополнительный observability event.
- Добавлен `docs/audits/PROMPT_BUILD_AUDIT_1_0_1.md` — composition
  map, baseline-замер (12 455 символов на типовом web turn'е,
  `persona_core` ~63%, `respond_directive` ~13%), список найденных
  дублирований (no-assistant / no-emoji / женский род / greeting ban /
  personality cli vs web) как target для 1.0.2–1.0.3.
- Тесты: `tests/test_1_0_1_prompt_build_event.py` — событие эмитится,
  лейблы в `PROMPT_BLOCK_LABELS`, сумма размеров блоков совпадает с
  длиной возвращённого prompt'а, нет утечки сырого текста, cli/web
  source-флаг прокидывается. Полный suite зелёный; актуальный счёт —
  смотри в CI.

### 1.0.2 — Prompt Fragment Registry

- Новый пакет `core/prompts/fragments/`:
  - `registry.py` — `Fragment` / `Recipe` / `FragmentRegistry` +
    `assemble()`. Fragment immutable (frozen dataclass), Registry
    отвергает дубль id (`DuplicateFragmentError`) и падает на missing
    eagerly (`MissingFragmentError`). `assemble()` дедуплицирует по
    id, сортирует по priority desc (stable), join'ит — детерминированно.
  - `library.py` — канонические фрагменты, выведенные из dedup-находок
    1.0.1: `no_unicode_emoji`, `no_assistant_voice`, `gender_feminine`,
    `messenger_short_format`, `greeting_ban_web` / `greeting_ban_cli`,
    `gender_feminine_reminder`. Семантические priority-константы
    (INVARIANT_PRIORITY=100, SITUATIONAL_PRIORITY=50, TONE_PRIORITY=10).
  - `recipes.py` — seed-набор: `voice_invariants` (every-turn rules),
    `greeting_ban_web` / `greeting_ban_cli`, `respond_directive_tail`.
- Никакой интеграции с `core/engine/prompt_builder.py` в этом стейдже
  — foundation-only. Active path не тронут, поведение prompt'а не
  меняется. Реальная подмена троекратных повторений (no-emoji, no-
  assistant, gender) на recipe-сборку — задача 1.0.3 (Persona Prompt
  Compression).
- Тесты: `tests/test_1_0_2_prompt_fragment_registry.py` (27 кейсов) —
  immutability, dedup, priority sort + stable order, missing-id
  raises, library-инварианты (id'ы уникальные, priorities на местах,
  теги ожидаемые), seed-recipes ссылаются только на known ids,
  `voice_invariants` ассемблится один раз без дублей.
- Документация: `docs/audits/PROMPT_FRAGMENT_REGISTRY_1_0_2.md`
  (дизайн, library inventory, стабильность). Полный suite зелёный;
  актуальный счёт — смотри в CI.

### 1.0.3 — Persona Prompt Compression

Первая реальная интеграция registry в active path.

- `core/engine/prompt_builder.py`: shared `_FRAGMENT_REGISTRY`
  собирается один раз на module-load. Новый канонический блок
  `voice_invariants` эмитится сразу после `technical_rules` и
  содержит `assemble(VOICE_INVARIANTS, registry)` под заголовком
  `# ИНВАРИАНТЫ ГОЛОСА`. `PROMPT_BLOCK_LABELS` расширен.
- Inline-строки заменены на recipes:
  - `greeting_ban` (web vs cli) → `GREETING_BAN_WEB_RECIPE` /
    `GREETING_BAN_CLI_RECIPE`;
  - хвост `respond_directive` с напоминанием о женском роде →
    `RESPOND_DIRECTIVE_TAIL`.
- Сжаты три исторически дублирующих источника правил:
  - `CORE_PERSONA` — удалены секции «ЖЕНСКИЙ РОД», «# ФОРМАТ»
    (1–2 предложения), параграф про unicode emoji из «# СТИЛЬ».
    Остались персона-specific бллоки (декораторы, запреты на пустые
    реакции, поведение).
  - `MARIA_VOICE_RULES` — из семи bullet'ов осталось два (русский
    язык + допустимая разговорная лексика). Остальное теперь приходит
    из registry.
  - `FINAL_RESPONSE_TECHNICAL_RULES` — удалены «no generic assistant»
    и «no unicode emoji» (теперь invariants); остались технические
    инструкции (русский по умолчанию, English-only-on-request, hidden
    control instructions).
- `locked_preferences` уже context-triggered — `build_active_locked_preferences_block`
  принимает `active_anchor_context` и эмитит блок только если профайл
  возвращает непустую строку. На public-template — всегда `""`.
  Stage 1.0.3 этот контракт подтвердил, не менял.
- Замер до/после на типовом web turn'е: 12 435 → 11 964 chars
  (−471, ~−3.8 %). Каждое канонически правило теперь встречается в
  prompt'е **ровно один раз** (раньше — 2–3).
- Тесты: `tests/test_1_0_3_persona_compression.py` (10 кейсов) —
  `voice_invariants` label канонический, блок эмитится на web и cli,
  все четыре канонически фрагмента присутствуют по одному разу,
  заголовок `# ИНВАРИАНТЫ ГОЛОСА` есть, greeting-ban отсутствует на
  первом turn'е и появляется на followup'ах в правильной cli/web
  формулировке без перекрёстных утечек, gender reminder — один и
  только из recipe.
- Документация: `docs/audits/PROMPT_COMPRESSION_1_0_3.md` (что
  ужалось, замеры до/после, гарантии дедупа, статус locked
  preferences). Полный suite зелёный; актуальный счёт — смотри в CI.

### 1.0.4 — Character-aware Capability Replies

Новый тонкий слой `CharacterHelpMode` — capability-ответы больше не
звучат как обезличенный assistant. Стейдж отвечает на вопрос «как
помощь должна ощущаться», который раньше не задавался отдельно от
handler-формы и policy-decision'а.

- `core/capabilities/character_help_modes.py` — pure selector
  `select_help_mode(analysis)`. Без I/O, без мутаций, без LLM. Четыре
  канонически режима: `reluctant_help` (cold/shutdown/irritated +
  practical), `curious_help` (reserved/cautious + practical),
  `boundary_help` (refusal или unknown_capability — независимо от
  стадии), `warm_help` (comfort/close/very_close + practical).
  Неизвестная новая стадия → `curious_help` (нейтральный fallback).
- Четыре новых фрагмента в `core/prompts/fragments/library.py`
  (`cap_help_reluctant` / `_curious` / `_boundary` / `_warm`) +
  по recipe на каждый mode (`CAP_HELP_*_RECIPE`) + dict
  `CAP_HELP_MODE_RECIPES` для lookup. Priority — situational;
  tags — `("capability", "help_mode", "situational")`.
- `core/engine/prompt_builder.py`: новый канонически label
  `capability_help_mode` в `PROMPT_BLOCK_LABELS`. Блок эмитится
  **только** когда `select_help_mode(current_analysis)` вернул
  не-None — на чисто-чатовых turn'ах prompt остаётся прежним.
  Позиция — после `maria_voice_rules`, перед `persona_compliance`.
  Заголовок включает mode-id: `# КАК ПОМОГАТЬ В ХАРАКТЕРЕ ({mode})`.
- Существующие `CapabilityHandler`-классы, policy и
  `enrich_capability_handler_guidance` — не тронуты. Mode сидит над
  ними как tone overlay, не заменяя `prompt_lines` / `next_step`.
- Никаких новых state-полей. Mode пересчитывается на каждом turn'е
  из уже посчитанного analysis. Никаких LLM-вызовов.
- Тесты: `tests/test_1_0_4_capability_help_modes.py` (23 кейса) —
  selector matrix (все четыре ветки + fallback), canonical inventory
  (`CHARACTER_HELP_MODES` ≡ 4 режима, каждый имеет recipe), prompt
  блок отсутствует на чат-turn'ах и эмитится с правильным заголовком
  на capability-turn'ах, нет перекрёстной утечки текстов режимов,
  refusal в very_close → boundary (стадия не перебивает refusal),
  позиция блока стабильна, фрагмент в prompt'е ровно один.
- Документация: `docs/audits/CAPABILITY_HELP_MODES_1_0_4.md`
  (проблема, правило выбора, интеграция, что не менялось, следующие
  шаги). Полный suite зелёный; актуальный счёт — смотри в CI.

### 1.0.5 — Opinion Schema v2

Foundation-only. Stage даёт типизированный язык для будущей
opinion-системы (которая будет переписана в 1.1.x foundation
rewrite), не трогая текущую storage / miner.

- `core/brain/opinion_schema.py` — frozen `OpinionStatement`
  dataclass и шесть канонически statement_types:
  `USER_PREFERENCE`, `MARIA_PREFERENCE`, `USER_BELIEF_ABOUT_MARIA`,
  `TOPIC_EXPERIENCE`, `TOPIC_QUESTION`, `SHARED_TOPIC_INTEREST`. Плюс
  набор `REASON_*` кодов, объясняющих, какое правило сработало.
- `classify_statement(perception_signals, extractor_signals)` — pure
  deterministic classifier. Строгий precedence order, leading-belief
  about Maria выигрывает над всем.
- **Главный инвариант 1.0.5**: leading-question, оформленный как
  утверждение о Maria, никогда не становится `MARIA_PREFERENCE`.
  Даже если в analysis'е одного turn'а одновременно есть
  `user_belief_about_maria_topic` и `maria_preference_topic` на
  одну и ту же тему, выигрывает belief. Maria не ассимилирует
  приписанные ей предпочтения. Тест
  `test_leading_belief_cannot_become_maria_preference_even_with_maria_topic`
  пинит это намертво.
- Никакой интеграции с storage / miner / active path. Это
  foundation в духе 1.0.2 fragment registry — runtime получит
  schema, когда 1.1.x перепроектирует opinion-систему целиком.
- Тесты: `tests/test_1_0_5_opinion_schema_v2.py` (17 кейсов) —
  inventory из 6 типов, frozen-контракт `OpinionStatement`,
  валидация unknown type / out-of-range position / confidence,
  graceful обработка None / {} / partial keys, классификация
  каждой из шести категорий + leading-belief precedence (не
  становится MARIA_PREFERENCE и подавляет USER_PREFERENCE на ту же
  тему), topic_question выигрывает над user_preference, evidence
  пропагируется.
- Документация: `docs/audits/OPINION_SCHEMA_V2_1_0_5.md` (категории,
  contract, precedence, главный инвариант, связь с 1.1.x).
  Полный suite зелёный; актуальный счёт — смотри в CI.

### 1.0.6 — Memory and Vector Refinement

Foundation-only. Stage даёт явный retention-policy контракт, который
разделяет dialogue vector buffer от long-term worthy memories, и
формализует поведенческий инвариант защиты `mari_memories` от
short-term churn'а. Runtime trimmer не тронут — это foundation,
который 1.1.x memory rewrite адоптит.

- `core/memory/retention_policy.py` — два логических bucket'а:
  `DIALOGUE_BUFFER` (user / reply / thought + unknown categories) и
  `WORTHY_MEMORY` (mari_memory). `categorize_entry(entry)` принимает
  оба формата storage'а (v1 `category=...`, v2 `source=...`).
  Frozen `RetentionPolicy` с per-bucket quotas + `total_hard_cap`
  (последний может быть жёстче per-bucket, действует как final
  safety net).
- `apply_retention(entries, policy)` — pure helper. Алгоритм:
  split по bucket → per-bucket overflow drops oldest-first →
  combined hard cap drops **только** из dialogue_buffer
  (worthy_memory **никогда** не evictится hard cap'ом) → возврат в
  chronological order.
- **Главный инвариант 1.0.6**: worthy memory **никогда** не
  выкидывается dialogue churn'ом или combined hard cap'ом. Это
  даёт `mari_memories` поведенческий статус long-term anchor'а, в
  отличие от текущего legacy trimmer'а, который дропает их первыми.
- Никакой интеграции с `vector_memory.py` / `vector_storage_v2.py` /
  worthiness gate. Storage shape и runtime trimmer не тронуты.
- Тесты: `tests/test_1_0_6_memory_retention.py` (17 кейсов) —
  inventory из двух bucket'ов, categorization для всех known
  categories + v2 source field + unknown fallback, policy
  validation (negative quotas rejected, hard-cap-tighter-than-quotas
  допустима), `apply_retention` handles None/empty, under-quota
  preserves everything, dialogue overflow → drops oldest dialogue,
  worthy NEVER evicted by dialogue churn (критический инвариант),
  worthy overflow → drops oldest worthy, hard cap → только dialogue
  выбивается, extreme hard cap может выбить все dialogue но worthy
  стоят, `split_by_bucket` always returns both keys, chronological
  order preserved.
- Документация: `docs/audits/MEMORY_RETENTION_1_0_6.md` (проблема,
  два bucket'а, contract, главный инвариант, связь с 1.1.x).
  Полный suite зелёный; актуальный счёт — смотри в CI.

### 1.0.7 — Actor Model & Scene Affordances Follow-up

Закрывающий стейдж линии Rebirth 1.0.x. Добавляет программный
выбор аффорданса (helper + observability + prompt rendering),
оставляя active-path wiring задачей 1.1.x foundation rewrite.

- `core/perception/affordance_choice.py` — новая функция
  `pick_active_affordance(frame, *, recent_choices, persona_weights,
  seed)`. Тонкий wrapper над `choose_affordance`, принимающий
  PerceptionFrame целиком. Эмитит privacy-safe `affordance_chosen`
  runtime event (`chosen`, `available_count`, `recent_choices_count`
  — без сырого текста). По построению не может выбрать из
  `hard_constraints` / `avoid` — программная подсказка работает
  только с soft affordances.
- `core/engine/reaction_intent.py`:
  `format_perception_frame_prompt_block(frame, *,
  chosen_affordance=None)` и `append_perception_frame_to_prompt(...,
  *, chosen_affordance=None)` получили optional kwarg. Когда передан
  и chosen в текущих `frame.affordances`, в блок добавляется
  "Программный выбор: {chosen} (ориентир, не команда — можно
  сымпровизировать в его духе)". Defensive guard защищает от stale
  callers, передающих pick от прошлого turn'а.
- **Главный инвариант 1.0.7**: программная подсказка не может стать
  механизмом обхода hard constraints. По построению pick идёт
  только из `frame.affordances`; защита сформализована тремя
  тестами (returns one of frame's affordances, never selects from
  hard_constraints, block ignores chosen outside affordances).
- Никакого active-path wiring. Helper, prompt rendering и event
  emission готовы; вызов из `core/engine/brain.py` / TurnExecutor —
  часть 1.1.x foundation rewrite, где perception flow станет
  `SceneInput → MariaPerception → FeelingFrame → ReactionSpace →
  ResponseAct` и `pick_active_affordance` войдёт в естественный
  ReactionSpace → ResponseAct переход.
- Тесты: `tests/test_1_0_7_programmatic_affordance_choice.py`
  (14 кейсов) — pick returns affordance из frame; None для пустого;
  детерминированность при seed; **critical invariant** never
  selects from hard_constraints; event эмитится / не эмитится
  корректно; recent_choices_count пропагируется; блок omits/renders
  "Программный выбор" в соответствующих случаях; defensive ignore
  для stale chosen; empty frame → empty block даже с chosen;
  append-helper пропагирует kwarg; backward-compat без kwarg;
  registry inventory invariants.
- Документация: `docs/audits/AFFORDANCE_CHOICE_1_0_7.md` (контекст,
  что добавлено, что не менялось, главный инвариант, observability,
  что не интегрировано пока, связь с 1.1.x).
- `Project_state.md` обновлён: branch отметка переключена с
  `direction/new-horizon-0.20.4.x-improvements` на закрытие
  `rebirth/1.0.x`, добавлены упоминания 1.0.x foundation
  деливераблов (fragment registry, opinion schema v2, memory
  retention policy, programmatic affordance choice).
- Полный suite зелёный; актуальный счёт — смотри в CI.

---

**Линия Rebirth 1.0.x закрыта.** Следующая ветка —
`rebirth/1.1.x-new-foundation` по handoff'у
`to_pass/REBIRTH_1_1_NEW_FOUNDATION_CLAUDE_CODE_ROADMAP.md`
(FastAPI bridge, single-user Core, persona split, внешние клиенты
для Voice/Live2D/CV).
