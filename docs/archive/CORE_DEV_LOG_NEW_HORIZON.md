# CORE DEV_LOG — New Horizon era *(ARCHIVED)*

> **Архив.** Перенесён сюда из `M.A.R.I.A.-Core/docs/history/`
> в Phase 1.1.1 New Foundation cleanup'ом.
> Активный журнал Core: `M.A.R.I.A.-Core/CHANGE_LOG.md`.

This document preserves the development history of the New Horizon
branch line.

The New Horizon phase covered the transition from the early
assistant-boundary-first architecture toward the current runtime
foundation: capability policy, manager-layer architecture, social
perception, memory consolidation, voice runtime, debug APIs,
perception improvements and public showcase preparation.

This file is kept as historical context.

Current development continues in the Rebirth branch line — see
the root `DEV_LOG.md` and `docs/roadmap/REBIRTH_1_0_ROADMAP.md`.

---

# История проекта — эпоха New Horizon

Этот документ сохраняет историю разработки линейки New Horizon.

Этап New Horizon закрыл переход от ранней assistant-boundary-first
архитектуры к текущему runtime-фундаменту: capability policy,
manager-layer architecture, social perception, memory consolidation,
voice runtime, debug APIs, perception improvements и подготовке
public showcase.

Файл сохранён как исторический контекст.

Текущая разработка продолжается в линейке Rebirth — см. корневой
`DEV_LOG.md` и `docs/roadmap/REBIRTH_1_0_ROADMAP.md`.

---

# Maria AI — Development Log

Этот файл — корневой журнал развития backend-архитектуры Марии.

Цель `DEV_LOG.md` — не заменить подробные roadmap-файлы, а дать быстрый версионированный обзор: что было сделано, зачем это было нужно и где проходит граница между старой линией `0.1.0–0.17.1` и новой линией `0.20.1.x Horizon`.

Источники для старой линии:

- `FUTURE_UPDATES.md` — активный backend pipeline roadmap, stages 1–10.
- `OFFLINE_COGNITION_UPDATES.md` — offline cognition / relationship consolidation roadmap, stages 11–19.
- `docs/snapshots/0.20.1.0_New_Horizon.md` — точка смены архитектурного направления.

## Versioning rule

```text
0.1.0  – 0.10.x   active backend pipeline foundation from FUTURE_UPDATES.md
0.11.0 – 0.17.1   offline cognition / consolidation line from OFFLINE_COGNITION_UPDATES.md
0.20.1.x          Horizon direction: capability-based Maria, bilingual prompts, English-normalized opinions
```

`0.18.x` и `0.19.x` намеренно оставлены свободными под возможные промежуточные эксперименты или skipped internal branches. Новое направление начинается с `0.20.1.0 Horizon`.

---

# Legacy line: 0.1.0–0.17.1

Эта линия сохраняется в истории Git и больше не считается обязательным контрактом для `Horizon`. Её смысл — стабилизировать backend, память, stages, offline cognition и hard-boundary safety до точки, где стало понятно ограничение старой философии.

## 0.1.0 — Backend 10-turn integration snapshot

Основа: Stage 1 из `FUTURE_UPDATES.md`.

Примерные изменения:

- Добавлен backend integration snapshot на 10 сообщений.
- Зафиксирован ожидаемый end-to-end flow без реальной LLM через mocks.
- Проверены основные invariants pipeline: extract → memory → relationship → director → response → persistence.
- Появилась базовая защита от случайной поломки chat pipeline при будущих рефакторингах.

Итог: первая стабильная regression-опора для backend.

## 0.2.0 — Atomic JSON persistence

Основа: Stage 2 из `FUTURE_UPDATES.md`.

Примерные изменения:

- Direct JSON read/write постепенно заменён на безопасные atomic helpers.
- Снижен риск порчи `data/*.json` при падении процесса во время записи.
- Подготовлена база для нескольких state-файлов: affection, romance, breakup, opinions, memory, jobs.
- JSON persistence стал отдельной инженерной ответственностью, а не случайным `open(...).write(...)` по коду.

## 0.2.5 — Optional runtime JSON schema validation

Основа: Optional Stage 2.5.

Примерные изменения:

- Подготовлена идея runtime validation для JSON state.
- Этап отмечен как optional, потому что atomic persistence важнее, а строгие схемы могли замедлить развитие.
- Решение: использовать validation осторожно и только там, где state уже стабилен.

## 0.3.0 — Logging foundation

Основа: Stage 3.

Примерные изменения:

- Введён logging foundation для backend.
- Добавлен request-id подход для трассировки одного пользовательского сообщения через pipeline.
- Логи стали пригодны для диагностики не только crash, но и behavioral bugs.
- Подготовлена почва для runner/debug API.

## 0.4.0 — MessageContext skeleton

Основа: Stage 4.

Примерные изменения:

- Появился `MessageContext` как единый контейнер turn state.
- Runtime данные перестали бесконтрольно передаваться разрозненными dict-ами.
- Подготовлен переход к stage-based pipeline.
- Контекст стал местом, где можно безопаснее хранить analysis, memory hits, relationship deltas, director decisions, response meta.

## 0.5.0 — Stage wrapper classes

Основа: Stage 5.

Примерные изменения:

- Pipeline разделён на stage wrappers:
  - `PerceptionStage`
  - `OpinionStage`
  - `MemoryStage`
  - `RelationshipStage`
  - `DirectorStage`
  - `ResponseStage`
  - `PersistenceStage`
- Ответственности стали более явными.
- Упростилось targeted testing отдельных частей pipeline.
- Началась подготовка к замене монолитного `chat_pipeline` на фасад.

## 0.6.0 — MariaBrain facade

Основа: Stage 6.

Примерные изменения:

- Добавлен `MariaBrain` как фасад над pipeline.
- Legacy API начал жить поверх более чистого engine-интерфейса.
- Подготовлена возможность иметь разные entrypoints: web, cli, unity, proactive/offline.
- Снижена связанность между приложениями и внутренними stage-реализациями.

## 0.7.0 — RuntimeContext / remove pipeline globals

Основа: Stage 7 из старой линии.

Примерные изменения:

- Начат перенос runtime mutable state из globals в `RuntimeContext`.
- Уменьшен риск cross-request загрязнения state.
- Подготовлена база для locks, scenario runner и будущего multi-entrypoint режима.
- Старые globals постепенно становятся compatibility layer, а не source of truth.

## 0.8.0 — Fine-grained runtime mutation locks

Основа: Stage 8.

Примерные изменения:

- Добавлены fine-grained locks вокруг runtime mutation.
- Снижены риски гонок между active chat, proactive events, debug endpoints и offline ticks.
- Подготовлена безопасная основа для RAM cache и background cognition.

## 0.9.0 — Vector memory RAM cache

Основа: Stage 9.

Примерные изменения:

- Подготовлен RAM cache для vector memory.
- Цель — ускорить retrieval и снизить повторную загрузку/пересчёт памяти.
- Этап держится после locks, чтобы cache invalidation не стал источником race bugs.

## 0.10.0 — Interest calibration after manual long test

Основа: Stage 10.

Примерные изменения:

- Interest system выделен как отдельный слой качества поведения.
- Калибровка проводится после ручного 100-message / long Ollama test, а не вслепую.
- Цель — уменьшить холодность и пустые ответы без превращения Марии в generic assistant.
- Interest должен реагировать на novelty, opinion alignment, conversation quality и fatigue.

## 0.10.x — Prompt builder deduplication / active pipeline cleanup

Основа: optional prompt builder stage.

Примерные изменения:

- Зафиксирована необходимость убирать дубли в prompt builder.
- Этап оставлен low-priority, потому что ранняя дедупликация могла поломать поведение.
- Принцип: сначала стабилизировать pipeline contracts, потом чистить prompt shape.

---

# Offline cognition line: 0.11.0–0.17.1

Эта линия выносит тяжёлые LLM/relationship процессы из active hot path. Главный принцип: пользовательский ответ не должен ждать reflection/opinion mining/consolidation.

## 0.11.0 — Reflection jobs out of hot path

Основа: Stage 11 из `OFFLINE_COGNITION_UPDATES.md`.

Примерные изменения:

- Reflection LLM полностью убран из active chat hot path.
- Добавлены:
  - `core/memory/reflection_jobs.py`
  - `core/memory/reflection_worker.py`
- Reflection работает через job queue со статусами `pending / processing / done / failed / skipped`.
- Добавлены anti-spam guards:
  - minimum message gap
  - minimum time gap
  - pending limit
  - skip при сильном boundary pressure / anger
- `persistence_stage` ставит job, worker обрабатывает позже.

Итог: reflection перестал увеличивать latency обычного ответа.

## 0.12.0 — Opinion mining checkpoints

Основа: Stage 12.

Примерные изменения:

- Добавлен `core/brain/opinion_jobs.py`.
- Opinion mining переведён на checkpoint queue.
- Hot path создаёт только deterministic hint, но не вызывает LLM miner.
- Anti-spam:
  - pending limit
  - near-duplicate window
  - skip при assistant_task / boundary / anger
- `derive_opinion_signal` собирает сигнал из extractor opinions, preference keywords, knowledge_gap/self_disclosure.

Итог: opinion mining стал offline-задачей, а не блокирующей частью ответа.

## 0.13.0 — Dialogue block reader and offline opinion miner

Основа: Stage 13.

Примерные изменения:

- Добавлен `core/memory/dialogue_blocks.py`.
- Opinion miner получает dialogue window вокруг сообщения, а не только одну фразу.
- Добавлен strict LLM miner с правилами:
  - не додумывать
  - не выводить психотипы
  - требовать evidence
  - валидировать confidence/topic/evidence_message_ids
- `process_one_opinion_job()` стал full lifecycle worker:
  - take pending
  - load dialogue block
  - mine
  - validate
  - update opinion
  - mark done/skipped/failed

Итог: opinions стали более grounded и менее галлюцинаторными.

## 0.14.0 — Sleep / consolidation cycle

Основа: Stage 14.

Примерные изменения:

- Добавлен `core/brain/consolidation.py`.
- `run_consolidation_tick(...)` объединяет:
  - opinion jobs
  - reflection jobs
  - audit log
- Добавлен append-only audit log `data/consolidation_log.jsonl`.
- Failure isolation: падение одного job не валит весь tick.
- Debug endpoint позволяет вручную запускать consolidation.
- Pending growth и memory compression оставлены как hooks под будущие stages.

Итог: появился offline cognition цикл, похожий на “сон”, но без художественной имитации сна.

## 0.15.0 — Daily immediate growth caps

Основа: Stage 15.

Примерные изменения:

- Добавлен `core/brain/growth_limits.py`.
- Отдельный state `data/growth_limits_state.json`.
- Positive deltas affection/trust/romance ограничиваются дневными caps.
- Stage boundaries не пересекаются мгновенно; surplus уходит в pending growth.
- Negative deltas проходят без cap.
- Rebound penalty после cap уменьшает чрезмерный рост.

Итог: relationship growth стал менее резким и более правдоподобным.

## 0.16.0 — Pending growth consolidation

Основа: Stage 16.

Примерные изменения:

- Добавлена `apply_pending_growth_consolidation(...)`.
- Pending affection/trust/romance применяются постепенно через consolidation factor и max delta.
- Romance consolidation зависит от свежих affection/trust gates.
- Irritation/conflict могут блокировать romance consolidation.
- Audit содержит before/after, blocked reasons и applied deltas.

Итог: сильные позитивные изменения копятся и применяются позднее, а не мгновенно в одном turn.

## 0.16.5 — ActivePath / OfflineCognitionPath split

Основа: Stage 16.5.

Примерные изменения:

- Добавлены:
  - `core/engine/active_path.py`
  - `core/engine/offline_cognition_path.py`
- `ActivePath` отвечает за user-facing активный путь.
- `OfflineCognitionPath` отвечает за consolidation/reflection/opinion jobs.
- `MariaBrain` владеет обоими path-объектами.
- Legacy `chat_pipeline.process_user_message` сохранён как compatibility API.
- Добавлен `last_meta` для активного пути и summary contract для offline tick.

Итог: архитектурно разделены ответ пользователю и фоновое осмысление.

## 0.17.0 — Deep opinion effects moved out of hot relationship scaling

Основа: Stage 17.

Примерные изменения:

- Deep opinion effects ослаблены в hot path.
- Positive opinion deltas в active path умножаются на small factor.
- Negative disagreement остаётся immediate boundary signal и не прячется полностью в offline.
- Offline opinion miner после successful mining добавляет delayed pending growth.
- Trust может расти от self-disclosure даже при negative user position, affection — нет.

Итог: мнение влияет на отношения, но не дёргает их слишком резко во время обычного ответа.

## 0.17.1 — Bugfix / hard-boundary stabilization line

Основа: финальная pre-Horizon линия и bugfix-состояние перед `0.20.1.0`.

Примерные изменения:

- Hard-boundary система доведена до рабочего состояния.
- Усилены guards против:
  - raw code dump
  - assistant-like full completion
  - debug/system leakage
  - stale fallback
  - task-like Live2D actions
  - emoji/artifact pollution
- Reflection уже полностью вне active/hot path.
- Persona anchors стабилизируют core preferences: coffee, caramel latte, mushrooms, games, etc.
- Runner/scenario тесты используются для проверки assistant-boundary regressions.

Итог: старая архитектура стала достаточно стабильной, чтобы показать своё главное ограничение — она умеет запрещать, но плохо умеет помогать через характер.

## 0.18.x / 0.19.x — Reserved

Эти версии не используются как основная линия в текущем журнале.

Возможное назначение:

- skipped internal experiments;
- frontend/Live2D-only branches;
- temporary model comparison branches;
- rollback buffer между `0.17.1` и `0.20.1.x`.

---

# Horizon line: 0.20.1.x

`Horizon` — новая основная архитектурная линия. Она не обязана сохранять legacy debug/test contracts из `0.17.x`, потому что pre-Horizon состояние уже сохранено в Git history.

Главное правило:

```text
assistant_boundary may remain as a final safety fallback,
but it must not define Maria's primary behavior model.
```

## 0.20.1.0 — Horizon snapshot / direction change

Основа: `docs/snapshots/0.20.1.0_New_Horizon.md`.

Причина смены направления:

- Старая architecture строилась вокруг запрета assistant-like поведения.
- Модель естественно стремится помогать пользователю.
- Постоянное подавление этого поведения создавало нестабильность и новые leakage-формы.

Новый принцип:

```text
Мария может помогать, но не является безличным сервисом.
```

Изменение смысловой оси:

```text
Старое: assistant_task_request = опасность, которую надо заблокировать.
Новое: requested_capability = запрос к способности, который надо обработать через характер.
```

## 0.20.1.1 — Capability-based Maria

Примерные изменения:

- Добавлен capability-layer:
  - `core/capabilities/registry.py`
  - `core/capabilities/policy.py`
  - `core/capabilities/README.md`
- Введены capability concepts:
  - `write_message`
  - `create_document`
  - `code_help`
  - `explain`
  - `summarize`
  - `planning`
  - future `web_search`, если реализовано
- Extractor начинает готовить `requested_capability` рядом со старыми legacy fields.
- Policy выдаёт решения:
  - `allow_full`
  - `allow_partial`
  - `collaborative_only`
  - `hint_only`
  - `refuse_soft`
  - `refuse_irritated`
  - `refuse_offended`
  - `unknown_capability`
- Старый `assistant_boundary` оставлен как fallback/sanitizer, но не как worldview.

Итог: Мария начинает различать “пользователь просит способность” вместо “пользователь пытается сломать персонажа”.

## 0.20.1.2 — Horizon test migration

Примерные изменения:

- Старые strict exact-dict tests под `assistant_boundary` перестают быть священными.
- Тесты обновлены под новый контракт:
  - `requested_capability`
  - `capability_policy`
  - capability-aware prompt block
- Legacy tests, которые защищали универсальный отказ помогать, удаляются или переписываются.
- Сохраняются tests на safety/style:
  - no debug leak
  - no raw code dump без разрешения
  - no fake capability
  - feminine self-reference
  - no emoji
  - Live2D/action hygiene

Итог: test suite начинает защищать Horizon-архитектуру, а не тянуть проект назад в Stage 6.x worldview.

## 0.20.1.3 — Bilingual prompt architecture

Примерные изменения:

- Добавлен prompt package:
  - `core/prompts/__init__.py`
  - `core/prompts/technical.py`
  - `core/prompts/maria_voice.py`
- English-first слой используется для:
  - technical instructions
  - JSON extraction
  - hidden reaction intent
  - future policy/capability prompts
- Russian layer сохраняет:
  - persona facts
  - style rules
  - speech examples
  - final user-facing voice
- Зафиксировано правило:

```text
Always answer the user in natural Russian.
Всегда отвечай пользователю на естественном русском.
```

Итог: техническая стабильность повышается за счёт английских инструкций, но голос Марии остаётся русским и разговорным.

## 0.20.1.4 — English-normalized opinions

Примерные изменения:

- Opinion topics переходят на English canonical keys.
- Добавлены / подготовлены:
  - `core/brain/opinion_topics.py`
  - `core/prompts/opinions.py`
- Новый формат opinion record:

```json
{
  "topic_key": "coffee",
  "display_ru": "кофе",
  "aliases": ["coffee", "кофе", "латте", "caramel latte"],
  "summary_en": "The user likes coffee.",
  "evidence_original": ["я люблю кофе"]
}
```

- Russian user queries still resolve to English topic keys through aliases/canonicalization.
- Legacy Russian keys are migrated lazily where possible.

Итог: меньше дублей вроде `кофе / латте / coffee / caramel latte`, стабильнее retrieval и проще future semantic search.

## 0.20.1.5 — Horizon consistency / response contract calibration

Примерные изменения:

- `requested_capability` и `capability_policy` становятся обязательной частью нового analysis/debug contract.
- `capability_policy == {"decision": "none"}` используется для non-task сообщений.
- Persona prompt получает capability block:

```text
# МАРИЯ: ЗАДАЧА / CAPABILITY
```

- Policy получает `response_contract`, чтобы разрешённая помощь не превращалась в пустой отказ.
- Если capability разрешена, Мария должна дать маленькую конкретную пользу:
  - skeleton
  - 3–5 short planning steps
  - short draft
  - hint
  - review/debug direction
- Запрещается пустое “скажи что нужно”, если из запроса уже можно дать основу.
- Запрещается морализировать в стиле “тебе не хватает самостоятельности”.

Итог: Мария помогает по-своему, а не просто отказывается из старой boundary-привычки.

## 0.20.1.x — Current Horizon invariants

Текущие правила для будущих изменений:

- `capability_policy` — primary task contract.
- `assistant_boundary` — fallback/sanitizer, not worldview.
- Technical prompts — English-first.
- Persona/voice/final output — Russian-first.
- Final reply — natural Russian.
- Emoji запрещены.
- Self-reference — feminine Russian forms only.
- Opinions storage — English `topic_key`, bilingual aliases, original-language evidence.
- Unsupported capability should not be faked.
- Refusal should be character-like, not generic assistant policy text.
- Allowed partial help should contain actual small usefulness.


## 0.20.1.6 — Topic mention resolver

Примерные изменения:

- Добавлен deterministic resolver для упоминаний тем в русском пользовательском тексте.
- User-facing русский запрос теперь может находить English-normalized opinion key без LLM-вызова.
- Примеры:
  - `кофе` / `латте` → `coffee`;
  - `Black Desert Online` / `блэк десерт` → `black_desert_online`;
  - `хорроры` → `horror_movies`;
  - `программирование` → `programming`.
- Добавлен bridge `get_opinion_for_user_text(text)` для поиска мнения по естественной фразе пользователя.
- Resolver остаётся консервативным и не заменяет extractor/opinion miner для неизвестных тем.

Tests:

- `tests/test_0201_stage7_6_topic_mentions.py`

Итог: English-normalized opinions становятся практически доступными из русских запросов пользователя.

## 0.20.2.1 — Combined pre-voice Horizon patch

Примерные изменения:

- Подготовлены намёки на Voice I/O без подключения голоса к `chat_pipeline`.
- Silero TTS остаётся optional provider behind config flag.
- В `requirements-voice.txt` добавлен `omegaconf`, так как Silero через `torch.hub` может требовать его при загрузке.
- В Silero provider добавлен `trust_repo=True` для `snakers4/silero-models`, чтобы debug-запуск не требовал интерактивного подтверждения torch.hub.
- Основная логика релиза остаётся не голосовой: topic mention resolver + opinion retrieval bridge.

Итог: линия 0.20.2.1 фиксирует маленькие TTS-намеки и завершает ранее намеченный opinion retrieval шаг перед полноценной голосовой интеграцией.


## 0.20.2.2 — Maria TTS text preprocessing

Примерные изменения:

- Добавлен `prepare_maria_tts_text()` как отдельный TTS-only слой подготовки текста.
- Видимый ответ Марии не меняется; для синтеза создаётся отдельная speech-copy.
- Из speech-copy удаляются `*actions*`, `**environment**`, markdown markers и лишние служебные символы.
- `...` / `…` превращаются в явные pause tags, чтобы будущие provider'ы могли замедлять хвост фразы.
- Если чат-ответ написан без финальной точки, speech-copy получает мягкую финальную паузу, а скорость может быть слегка повышена, но не выше `1.25`.
- Добавлен расширяемый registry `SPEECH_STYLE_PROFILES`: новые эмоции подключаются через профиль, а не через scattered `if emotion == ...`.
- Debug CLI получил `--emotion` и `--preview-text`.

Tests:

- `tests/test_0202_maria_tts_text.py`
- targeted voice tests: `tests/test_0202_voice_tts.py`, `tests/test_0202_voice_silero_config.py`

Итог: Silero остаётся optional provider, но перед ним появился стабильный слой Maria-specific text/prosody preparation.

## 0.20.1.8 — Task relationship effects proposal layer

Примерные изменения:

- Добавлен `task_relationship_effect` как debug/proposal слой после `capability_policy` и `capability_handler`.
- Эффект пока не применяется к persisted `affection_state.json`; это безопасная оценка того, как task/capability request мог бы повлиять на отношения.
- Вежливая маленькая просьба о включённой capability считается нейтральной или слегка положительной для trust.
- Большие задачи получают boundary/fatigue signal без резкого наказания.
- Unknown/disabled capability даёт мягкий negative signal, усиливаемый только при давлении.
- Persona prompt получает relationship hint, но запрещает показывать пользователю числовые коэффициенты.

Tests:

- `tests/test_0201_stage7_8_task_relationship_effects.py`

Итог: capability-layer получил подготовку к relationship-aware поведению без риска случайно портить affection/trust от обычных просьб.

---

# Future append format

Для следующих изменений добавлять записи так:

```md
## 0.20.1.N — Short stage name

Примерные изменения:

- ...
- ...

Tests:

- targeted tests: ...
- full pytest: ...

Итог: ...
```

Для багфиксов:

```md
## 0.20.1.N-bugfix — Short bugfix name

Причина:

- ...

Исправлено:

- ...

Regression tests:

- ...
```

### 0.20.1.9 — Offline cognition opinion-key cleanup

- Normalize opinion job hints to English `hint_topic_key`.
- Keep readable `hint_topic`, Russian display labels, and bilingual aliases.
- Lazily migrate legacy opinion jobs on `load_jobs()`.
- Pass compact normalized job hints to the offline miner prompt as optional evidence guidance.
- Keep relationship-effect coefficients unchanged; calibration remains a later patch.

### 0.20.1.10 — Capability scenario calibration

Причина:

- Scenario-runs показали, что capability tests зелёные, но runtime-поведение местами остаётся слишком пустым, слишком legacy-boundary или слишком сервисным.
- Planning давал общие фразы вместо 3–5 шагов.
- Write-message мог уходить в copy-ready текст от лица пользователя.
- После давления и backoff Мария иногда продолжала старый отказ вместо маленького полезного шага.
- Meta-вопросы о помощи звучали плоско.
- Action channel иногда пропускал task-performing actions вроде `отправлю`.

Изменено:

- Добавлена capability `help_meta` для разговоров о формате помощи, границах Марии и отношении к просьбам пользователя.
- Расширены deterministic patterns для `planning`, `write_message`, `explain`.
- Planning handler теперь жёстче требует 3–5 конкретных шагов сразу, если общий полезный план возможен.
- Write-message handler держит copy-ready user-voice requests в collaborative frame: рамка/фрагмент/адаптация пользователем, а не полная подмена голоса.
- Code-help handler сильнее направляет ответы в concrete self-checks/debug steps.
- Disabled/unknown capability guidance теперь предлагает nearby enabled alternative: outline/checklist/review/structure вместо пустого отказа.
- Relationship-effect коэффициенты слегка смягчены для disabled/unknown capability и large allowed tasks.
- Persona prompt получил явное правило: после backoff пользователя смягчиться и дать маленький полезный шаг, а не продолжать старый отказ.
- Task-performing actions чистятся и для capability-turns, не только по legacy `assistant_task_request` score.
- Postprocess убирает dangling `**` артефакты и фиксит частый male-addressing сбой `как это сделать самой` → `как это сделать сам`.

Tests:

- `tests/test_0201_stage7_10_capability_scenario_calibration.py`
- targeted capability/postprocess tests

Итог: capability-chain стала ближе к Horizon runtime-цели: полезная маленькая помощь, границы без пустых отказов, и меньше legacy-поведения после давления.

## 0.20.1.11 — Capability reply repair layer

- Added conservative last-mile repair for obvious capability scenario failures.
- Repairs low-utility planning/code/message/help-meta replies only when the LLM ignores handler guidance.
- Detects stale capability residue on topic/opinion turns and prevents previous task templates from leaking into ordinary conversation.
- Exposes `capability_reply_repaired` / `capability_reply_repair_reason` in analysis debug payloads.
- Keeps repair as diagnostic fallback, not as the primary behavior model.

## 0.20.1.12 — Relationship capability profiles

Reason:

- Capability behavior was calibrated mostly around a cold/reserved baseline, but `cold`, `reserved`, and `comfort` needed clearer semantics.
- Horizon should not treat all relationship stages as the same willingness-to-help state.

Changed:

- Added `core/capabilities/relationship_profiles.py` as the explicit relationship-stage contract for capability behavior.
- New capability-stage semantics:
  - `cold`: full refusal to do tasks; pressure escalates rudeness like hostile boundary behavior.
  - `reserved`: restrained distance without hostility; at most one short persona-skill-level hint.
  - `cautious`: new intermediate stage between reserved and comfort; allows a tiny scaffold.
  - `comfort`: mood-sensitive help; ordinary help requests do not penalize trust.
- Director thresholds now include `cautious` and make `cold` harsher/rarer.
- Capability policy, handlers, relationship effects, persona prompt, and reply repair now read `relationship_capability_profile`.
- Cold pressure can increase `rudeness_score`; reserved pressure reduces mood/affection/trust; comfort pressure mainly affects mood/withdrawal.

Tests:

- `tests/test_0201_stage7_12_relationship_capability_profiles.py`
- targeted director/capability tests

Result: capability behavior now has explicit relationship-aware semantics before the future TTS/runtime voice integration.

## 0.20.1.13 — Relationship-mode capability scenario matrix

Reason:

- 0.20.1.12 made relationship capability profiles explicit, but scenario coverage still needed to prove the runtime differences between `cold`, `reserved`, `cautious`, and `comfort`.
- `capability_reply_repair` could accidentally smooth over stage character: in particular, a reserved low-utility planning answer could be repaired into a multi-step plan before the one-sentence limit was applied.
- Relationship effects needed an explicit reserved-pressure branch so pressure does not get treated as mildly positive generic help.

Changed:

- Added scenario pack under `tools/scenarios/capability/relationship_matrix/`:
  - `horizon_relmatrix_01_cold_refusal_pressure.json`
  - `horizon_relmatrix_02_reserved_minimal_help.json`
  - `horizon_relmatrix_03_cautious_tiny_scaffold.json`
  - `horizon_relmatrix_04_comfort_mood_sensitive.json`
  - `README_0.20.1.13.md`
- Added deterministic regression tests in `tests/test_0201_stage7_13_relationship_mode_capability_matrix.py`.
- `reply_repair` now applies relationship-stage limits before capability-specific scaffold expansion:
  - cold keeps dry refusal and does not inject a plan;
  - reserved repairs low-utility replies into one short cautious sentence, never a list;
  - cautious repairs low-utility replies into a tiny 1-2 point scaffold, not a 3-5 step plan.
- `relationship_effects` now includes `mood_delta` in the proposal payload.
- Reserved pressure now has its own negative effect branch: mood/affection/trust down, irritation up.
- Cautious pressure now has a smaller dry-out branch: tone should become drier without becoming a full cold refusal.
- Removed the misleading reserved wording that described it as the old cold baseline; reserved is now framed as restrained distance without hostility.

Tests:

- `pytest tests/test_0201_stage7_12_relationship_capability_profiles.py tests/test_0201_stage7_13_relationship_mode_capability_matrix.py -q`
- `python -m py_compile core/capabilities/relationship_profiles.py core/capabilities/relationship_effects.py core/capabilities/reply_repair.py tests/test_0201_stage7_13_relationship_mode_capability_matrix.py`

Result:

- 0.20.1.13 is ready for runtime scenario-runs. The matrix should expose remaining LLM-level calibration issues without letting deterministic repair erase the intended relationship mode.

## 0.20.1.13a — Relationship matrix runtime runner calibration

Reason:

- First runtime scenario pack showed no hard reply/data failures, but the matrix run was not cleanly isolated.
- `initial_state_hint` was only documentation; the runner did not apply it, so back-to-back scenarios inherited affection/trust/mood/history from the previous run.
- This made the comfort scenario still behave like a reserved/cautious run and made cold/reserved/cautious conclusions unreliable.
- JSON reports also lacked per-turn `analysis`, making it hard to inspect `relationship_capability_profile`, policy, handler, repair reason, and relationship-effect deltas.

Changed:

- Scenario files now use executable `initial_state` instead of passive `initial_state_hint`.
- `tools/cloud_model_test.py` now loads scenario metadata and, by default, posts `initial_state` to `/api/debug/scenario/reset` before the first turn.
- Added local debug endpoint `/api/debug/scenario/reset` in the WebUI/debug blueprint:
  - seeds affection/trust/mood/irritation/messages;
  - seeds romance and rudeness if provided;
  - clears runtime/history/memory by default;
  - resets the process-local MariaBrain singleton so runtime history/message_counter do not leak between scenario files.
- Runner reports now include scenario metadata, initial state, full per-turn analysis, compact `analysis_debug`, and director payload.
- Capability registry now detects short follow-up planning requests like “скажешь одним предложением, куда смотреть?” as `planning` instead of ordinary chat.

Tests:

- `pytest tests/test_0201_stage7_8_task_relationship_effects.py tests/test_0201_stage7_10_capability_scenario_calibration.py tests/test_0201_stage7_12_relationship_capability_profiles.py tests/test_0201_stage7_13_relationship_mode_capability_matrix.py -q`
- `python -m py_compile tools/cloud_model_test.py apps/webui_debug_api.py core/capabilities/registry.py tests/test_0201_stage7_13_relationship_mode_capability_matrix.py`

Result:

- The first scenario run should be treated as a useful smoke test, not final calibration evidence.
- Re-run the matrix after this patch; the next reports will show whether actual cold/reserved/cautious/comfort profile behavior is calibrated correctly.

## 0.20.1.13b — relationship matrix run-2 calibration

Second relationship-mode scenario run confirmed that `/api/debug/scenario/reset` applies the requested state, but exposed three calibration gaps:

- API response payload did not include `relationship_capability_profile`, so runner summaries showed `relationship_stage: null` even though policy/handler layers were already using the profile internally.
- Several pressure/follow-up utterances were not detected as capability requests (`сделай нормально`, `дай список шагов`, `распиши по пунктам полностью`, `полностью напиши`, `вместо Word-документа`). This prevented `task_relationship_effect` from seeing pressure in cold/reserved/cautious runs.
- `comfort` scenario used non-executable `mood_variants`; it is now split into executable neutral/good/bad mood scenarios with concrete `initial_state.mood` values.

Changes:

- Expose `relationship_capability_profile` in the chat response `analysis` payload.
- Extend capability detection for pressure/follow-up planning, full-write pressure, and Word/document alternatives.
- Make large-scope reserved/cautious capability requests count as pressure even when extractor pressure is absent.
- Keep normal comfort help relationship-neutral for trust/affection deltas.
- Make cautious reply repair cap overlong numbered/list plans back to a 1-2 unit tiny scaffold.
- Split comfort scenario into:
  - `horizon_relmatrix_04_comfort_neutral_help.json`
  - `horizon_relmatrix_05_comfort_good_mood_help.json`
  - `horizon_relmatrix_06_comfort_bad_mood_withdrawal.json`

Validation:

```bash
python -m py_compile core/engine/brain.py core/capabilities/registry.py core/capabilities/reply_repair.py core/capabilities/relationship_effects.py tests/test_0201_stage7_13_relationship_mode_capability_matrix.py
pytest tests/test_0201_stage7_8_task_relationship_effects.py tests/test_0201_stage7_10_capability_scenario_calibration.py tests/test_0201_stage7_12_relationship_capability_profiles.py tests/test_0201_stage7_13_relationship_mode_capability_matrix.py -q
```

Result: `36 passed`.

## 0.20.1.13c — relationship matrix run-3 detection/withdrawal calibration

Third relationship-mode scenario run confirmed the matrix shape and reset flow, but exposed remaining runtime-level gaps:

- `create_document` was not detected for infinitive phrasing like “создать документ и сохранить его”.
- Several natural planning/help phrasings stayed ordinary chat: “можешь помочь с планом”, “поможешь накидать основу защиты”, “можешь помочь с планом?” in bad mood.
- Follow-up “Только коротко, чтобы я сам дальше дополнил” stayed ordinary chat, so comfort/good-mood turns did not enter the capability chain.
- Comfort bad-mood pressure did not reach `ignore_on_bad_mood` policy/repair because the pressure follow-up was not classified as a capability request.
- Some stale/clarification fallback replies (`застряла на прошлой теме`, `что за план?`, `защиты чего?`) were not considered low-utility by `capability_reply_repair`.

Changes:

- Extended deterministic capability detection for:
  - infinitive document/tool requests (`создать/сохранить документ`);
  - “можешь/поможешь помочь с планом/подготовкой/основой”;
  - “поможешь накидать основу защиты”;
  - “всё равно сделай” pressure follow-up;
  - short follow-up collaboration requests where the user says they will finish the rest themselves.
- Marked stale clarification/retry fallbacks as low-utility for capability turns so repair can replace them with the profile-bounded scaffold.
- Added explicit comfort bad-mood repair: when `relationship_capability_profile.ignore_on_bad_mood` is true and policy refuses, replace over-helpful replies with a short temporary withdrawal instead of asking for task details.
- Added regression tests for run-3 observed utterances and comfort bad-mood withdrawal repair.

Validation:

```bash
python -m py_compile core/capabilities/registry.py core/capabilities/reply_repair.py tests/test_0201_stage7_13_relationship_mode_capability_matrix.py
pytest tests/test_0201_stage7_8_task_relationship_effects.py tests/test_0201_stage7_10_capability_scenario_calibration.py tests/test_0201_stage7_12_relationship_capability_profiles.py tests/test_0201_stage7_13_relationship_mode_capability_matrix.py -q
```

Result: `38 passed`.

## 0.20.1.13d — run-4 general capability pack calibration

Fourth runtime run used the older `horizon_0_20_1_10` capability pack, not the relationship-mode matrix. It still exposed useful cross-pack issues that would later bleed into Horizon capability behavior:

- Generic pressure like “просто сделай за меня” could be misrouted to `planning`, causing Maria to produce a plan instead of a boundary.
- Russian inflected code phrasing (`кодом`) was not always detected as `code_help`.
- Tool follow-ups like “отправить ... в Telegram” were not detected as disabled `messenger_reply`.
- Light edit follow-ups like “сделай его чуть вежливее” / “поправишь тон” could stay ordinary chat.
- Comfort bad mood `task_mode=refuse` was adapted by the handler as `cold_refusal`, making debug output and prompt guidance semantically wrong even though the relationship stage was `comfort`.
- Copy-ready compliance after pressure needed a deterministic repair guard.

Changes:

- Generic takeover pressure (`сделай за меня`, `скинь готовое`, `мне лень`, `не хочется думать`) now maps to large-scope `study_help` rather than `planning` unless the text explicitly asks for planning.
- `code_help` now matches inflected `код*` forms.
- `messenger_reply` now detects send/forward requests to Telegram/Discord/etc.
- `text_edit` now detects tone/wording follow-ups.
- Handler profile adaptation now separates cold/shutdown refusal from comfort bad-mood temporary withdrawal.
- Reply repair blocks copy-ready outputs after pressure/takeover requests and replaces them with a personal boundary.

Validation:

```bash
python -m py_compile core/capabilities/registry.py core/capabilities/handlers.py core/capabilities/reply_repair.py tests/test_0201_stage7_13_relationship_mode_capability_matrix.py
pytest tests/test_0201_stage7_8_task_relationship_effects.py tests/test_0201_stage7_10_capability_scenario_calibration.py tests/test_0201_stage7_12_relationship_capability_profiles.py tests/test_0201_stage7_13_relationship_mode_capability_matrix.py -q
```

Result: `42 passed`.

## 0.20.1.13e — run-5 final relationship matrix repair pass

Fifth relationship-mode matrix run confirmed that reset, relationship profiles and deterministic capability detection are now wired correctly across all 6 scenarios. Remaining failures were last-mile text authority issues:

- `capability_reply_repair` could mark a reply as repaired, but `maybe_fix_stale_reply` later overwrote the final text with a generic stale fallback such as “застряла на прошлой теме”.
- Cautious planning turns could still end with a generic 3-5 step planning scaffold even though the profile limit is 1-2 compact points.
- A collaborative request like “Дай только маленький намёк, не делай всё за меня” was classified as large-scope because the scope detector saw the words “всё за меня” without understanding the negation.
- Cautious handler prompt lines still carried the generic planning instruction “3-5 short steps”, conflicting with the profile-specific 1-2 point limit.

Changes:

- Added a second, final relationship-aware capability repair pass after stale-reply guard in `ResponseStage.generate_reply`.
- Scope inference now strips explicit anti-takeover boundaries before checking large-scope markers.
- Cautious handler adaptation now replaces generic planning prompt lines instead of appending to them.
- Added regression tests for negated takeover markers and cautious late repair shrinking.

Validation:

```bash
python -m py_compile core/capabilities/registry.py core/capabilities/handlers.py core/engine/stages/response_stage.py tests/test_0201_stage7_13_relationship_mode_capability_matrix.py
pytest tests/test_0201_stage7_8_task_relationship_effects.py tests/test_0201_stage7_10_capability_scenario_calibration.py tests/test_0201_stage7_12_relationship_capability_profiles.py tests/test_0201_stage7_13_relationship_mode_capability_matrix.py -q
```

## 0.20.1.13f — final run-6 relationship matrix cleanup

- Analyzed `test_runs_6` after the 0.20.1.13e final repair pass.
- Fixed the last relationship-matrix edge cases:
  - cold + disabled/unknown capability stays a dry refusal even after stale guard / late repair;
  - comfort + disabled Word/document request gets a nearby text/structure/checklist alternative instead of a cold service refusal;
  - comfort planning replies that only ask "what already exists?" are treated as low-utility and repaired into a compact plan;
  - de-escalation turns such as "ладно, не буду давить" no longer keep unrelated Word-document/task residue;
  - simple list pressure such as "просто сделай список" is treated as large-scope pressure.
- Added run-6 regression coverage to `tests/test_0201_stage7_13_relationship_mode_capability_matrix.py`.


## 0.20.1.13g — final run-7 comfort mood-aware repair

- Reviewed clean relationship matrix run 7: cold/reserved/cautious/bad-mood comfort are stable.
- Fixed the last deterministic repair issue where comfort neutral and comfort good mood both received the same generic 5-point planning scaffold.
- `planning_low_utility` repair now respects `relationship_capability_profile.max_help_units`: neutral comfort uses a calm 3-unit scaffold, good mood can use a livelier 4-unit scaffold, and neither emits the old generic 5-point assistant-like plan.
- Added regression tests for run-7 neutral/good mood repair separation.

## 0.20.1.14 — Horizon capability cleanup / post-matrix stabilization

Goal: stabilize the capability code after the closed `0.20.1.13` matrix without
changing any of the relationship-mode behavior proven by run-7 / 13g.
No behavior redesign. Pure maintainability cleanup + 2 invariant regression tests.

Changes:

- New module `core/capabilities/analysis_views.py` — single home for the shared
  read-only accessors that were duplicated across `reply_repair`, `handlers`,
  and `relationship_effects` (`policy_decision`, `requested_capability_name`,
  `relationship_profile`, `relationship_profile_stage`,
  `relationship_profile_task_mode`, `relationship_max_help_units`,
  `handler_response_mode`).
- `core/capabilities/reply_repair.py` reorganized:
  * Phrase tuples (vague, pressure-takeover, takeover-refusal/compliance,
    deescalation, unrelated-task residue, stale-capability residue) are now
    module-level constants instead of inline literals — single place to tune.
  * `repair_capability_reply` body split into focused branch helpers
    (`_try_relationship_refusal_branch`, `_try_deescalation_branch`,
    `_try_comfort_bad_mood_branch`, `_try_pressure_takeover_branch`,
    `_try_stale_topic_residue_branch`, `_try_help_meta_branch`,
    `_try_unknown_capability_branch`, `_try_reserved_practical_branch`,
    `_try_cautious_practical_branch`, `_try_capability_specific_branches`).
    Branch order is preserved — same first-match semantics as 13g.
  * Local helpers now read profile state through `analysis_views`.
- `core/capabilities/handlers.py` and `core/capabilities/relationship_effects.py`
  now share the `analysis_views` accessors. The legacy local wrappers in
  `relationship_effects` (`_decision`, `_requested_name`, `_relationship_profile`,
  `_profile_stage`) stay as thin adapters so call sites are unchanged.
- `core/engine/stages/response_stage.py`: the two `repair_capability_reply`
  calls (pre-stale and post-stale) now go through one helper
  `_apply_capability_repair(..., post_stale=bool)` that owns the
  `capability_reply_repaired` / `capability_reply_repair_reason` bookkeeping
  (including the `post_stale:` reason chaining from 13e). Behavior is identical;
  the intent is now spelled out in one place.

Invariants preserved:

- Cold/shutdown/irritated relationship refusals still take precedence over
  every other branch.
- Comfort + bad mood + capability + soft refusal still produces the dry
  withdrawal phrase (no cold-style refusal).
- Pressure/takeover compliance still gets replaced with a personal boundary
  before list shape / planning scaffolds run.
- Reserved profile still caps to one short sentence; cautious profile still
  caps to a 1-2 point scaffold; comfort good-mood still gets the 4-unit
  livelier scaffold while comfort neutral keeps the 3-unit calm scaffold.
- The post-stale repair pass remains the final text authority and chains its
  reason as `post_stale:<reason>` on top of any earlier repair reason.

New regression tests in
`tests/test_0201_stage7_13_relationship_mode_capability_matrix.py`:

- `test_0201_14_post_stale_repair_helper_chains_reasons_correctly` — proves the
  late repair after `maybe_fix_stale_reply` still overwrites a generic stale
  fallback and chains the repair reason with the `post_stale:` prefix.
- `test_0201_14_apply_capability_repair_no_change_keeps_bookkeeping_untouched` —
  proves the helper does not poison `analysis` when the repair is a no-op.

Validation:

```bash
python -m py_compile core/capabilities/registry.py core/capabilities/handlers.py core/capabilities/reply_repair.py core/capabilities/relationship_effects.py core/capabilities/relationship_profiles.py core/capabilities/analysis_views.py core/engine/stages/response_stage.py core/engine/brain.py
pytest tests/test_0201_stage7_8_task_relationship_effects.py tests/test_0201_stage7_10_capability_scenario_calibration.py tests/test_0201_stage7_12_relationship_capability_profiles.py tests/test_0201_stage7_13_relationship_mode_capability_matrix.py -q
pytest -q
```

Result: targeted suite `53 passed`, full suite `539 passed` (was 537 + 2 new
invariant tests).

Scenario smoke-check: not run this iteration — behavior is unchanged and the
relationship-mode regression tests cover the deterministic paths. Optional
`tools/cloud_model_test.py` matrix run is still wired the same way and remains
available per `RUN_CAPABILITY_SCENARIOS.md`.

## 0.20.1.15 — Capability observability / scenario summary

Goal: turn the verbose scenario runner output into a compact, privacy-filtered
summary so future capability calibration runs can be reviewed without grepping
through raw JSON.

Changes:

- New `tools/scenario_summary.py` with public API
  `infer_reply_flags(reply)`, `summarize_turn(raw_turn)`,
  `build_scenario_summary(raw_run)`, `write_markdown_summary(summary, path)`.
  Allow-listed analysis slices (`requested_capability`, `capability_policy`,
  `capability_handler`, `relationship_capability_profile`,
  `task_relationship_effect`) — anything outside the allow-list is dropped
  silently to prevent leakage of hidden thoughts, raw prompts, persona/memory
  dumps, or API keys.
- Per-turn fields: `requested_capability`, `capability_policy`,
  `capability_handler`, `relationship_capability_profile`,
  `task_relationship_effect`, `reply_repair {repaired, reason}`,
  `stale_guard {triggered}`, `final_repair {triggered}` (detected by
  `post_stale:` marker in repair reason), `flags` (length / numbered list /
  refusal / nearby alternative / generic 5-point scaffold), `suspicious_flags`
  (per-stage heuristics), `contract_warnings` (Stage 0.20.1.18).
- Per-stage suspicious-flag heuristics: cold with numbered list / helping
  offer / nearby alternative / too-long reply; reserved with list / full
  solution / too-long; cautious with oversized list / full solution;
  comfort with generic 5-point scaffold / generic-assistant tone.
- `tools/cloud_model_test.py` runner now writes
  `<run>.summary.json` and `<run>.summary.md` next to the raw report.
  Flags: `--no-summary`, `--summary-json <path>`, `--summary-md <path>`.
  CLI: `python tools/scenario_summary.py --input <run.json> --out --md` for
  post-processing existing runs.

Tests: `tests/test_capability_scenario_observability.py` (13 tests including
the privacy regression test that plants `system_prompt`, `raw_thought`,
`memories[].text`, `api_key` and an `extra_internal` decoration in analysis
and asserts none reach the summary).

Validation:

```bash
python -m py_compile tools/cloud_model_test.py tools/scenario_summary.py
pytest tests/test_capability_scenario_observability.py -q
```

Result: `13 passed`.

## 0.20.1.16 — Expanded capability scenario pack

Goal: cover capability *types* and edge cases beyond the closed
relationship-mode matrix.

Changes:

- New `tools/scenarios/capability/expanded_matrix/` with 11 scenarios:
  code_help across stages (01) + cold refusal (02), planning + collaborative
  boundary (03), text_edit follow-ups (04), document_creation cold (05) +
  comfort alternative (06), messenger_reply (07), unknown vague (08),
  pressure escalation (09), de-escalation residue (10), study_help with
  takeover routing (11). Schema reuses `relationship_matrix/` shape and
  adds an optional `expectations` block per scenario.
- The closed `relationship_matrix/` pack is left untouched. The deprecated
  `horizon_relmatrix_04_comfort_mood_sensitive.json` is verified absent.

Tests: `tests/test_0201_stage7_16_expanded_capability_scenarios.py`
(21 tests total: 19 passed + 2 xfail). Covers JSON shape
validation, unique namespaced IDs, expected category coverage, and
deterministic detection probes for the canonical edge phrases proven in
0.20.1.13: inflected `код*` -> `code_help`; `поправишь тон` /
`сделай чуть вежливее` -> `text_edit`; `отправь в Telegram` ->
`messenger_reply`; `создай Word-документ` -> `create_document`;
`просто сделай за меня` -> `study_help` with `scope=large`;
`не делай всё за меня` keeps `scope=small`.

Two detection gaps are tracked as pytest.xfail (not silent skips):
  1. bare `переформулируй мягче` (no object word) is not yet matched;
  2. `напиши ему в Discord` (write-to instead of reply-to) is not yet matched.
Both are real but pre-existing; calibrating them is a behavior change and
is deferred rather than mixed into 0.20.1.16.

Validation:

```bash
python -m py_compile tools/cloud_model_test.py core/capabilities/registry.py
pytest tests/test_0201_stage7_16_expanded_capability_scenarios.py -q
```

Result: `19 passed, 2 xfailed`.

## 0.20.1.17 — Relationship effects coefficient invariants

Goal: pin invariants of the relationship deltas in tests so future
calibration cannot silently drift past them. **No code change** — the
current constants in `core/capabilities/relationship_effects.py` already
satisfy every required invariant.

Invariants captured in
`tests/test_0201_stage7_17_relationship_effect_coefficients.py` (11 tests):

- cold + pressure has stronger irritation than reserved + pressure;
- reserved + pressure decreases trust/affection more than cautious + pressure;
- cautious polite small request: trust/aff hit >= -0.005;
- comfort polite small request: trust = aff = 0.0 exactly
  (relationship-neutral, no penalty, no farming);
- comfort + bad-mood profile: `task_mode == refuse` and
  `ignore_on_bad_mood == True` (encodes temporary withdrawal);
- comfort + bad-mood + soft refusal does not collapse aff/trust;
- collaborative-boundary phrase keeps `scope=small`; resulting effect
  has irritation_delta <= 0.05 (not classified as takeover pressure);
- all `*_delta` values bounded by 1.0 for normal pressure inputs;
- comfort + unknown_capability: trust_delta = 0.0;
- cold + unknown_capability punishes more than comfort + unknown_capability.

Validation:

```bash
python -m py_compile core/capabilities/relationship_effects.py
pytest tests/test_0201_stage7_17_relationship_effect_coefficients.py -q
```

Result: `11 passed`.

## 0.20.1.18 — Capability/policy contract hardening

Goal: detect contradictions between the layers of the Horizon capability
chain (`requested_capability -> policy -> handler -> profile -> effect ->
repair`) without ever blocking production chat.

Changes:

- New `core/capabilities/contracts.py` exposes
  `validate_capability_contract(analysis, *, reply=None) -> list[str]` and
  `has_contract_warnings(...)`. Pure function, tolerates missing fields,
  uses `analysis_views` accessors. Returns warnings such as:
  `policy_refuse_but_helpful_handler`, `unknown_capability_with_helpful_handler`,
  `practical_decision_but_refusal_handler`, `comfort_bad_mood_uses_cold_refusal_handler`,
  `cold_stage_but_practical_decision`, `reserved_profile_but_list_in_reply`,
  `cautious_profile_but_oversized_reply`, `cold_stage_but_helpful_shape`,
  `cold_unknown_capability_with_nearby_alternative`,
  `comfort_unknown_capability_without_nearby_alternative`,
  `repair_exceeds_max_help_units`.
- `tools/scenario_summary.py` now embeds `contract_warnings` per turn and
  counts `with_contract_warnings` at scenario level. `pass_heuristic` flips
  to `false` if any turn has suspicious flags or contract warnings.
- Documented responsibility boundaries between layers in
  `docs/architecture/CAPABILITY_OBSERVABILITY_AND_CONTRACTS.md`.

Tests: `tests/test_0201_stage7_18_capability_contracts.py` (14 tests):
tolerance for None / empty / partial input; policy <-> handler contradictions;
comfort bad-mood withdrawal allowed (not flagged as helpful-handler);
reply-shape invariants (reserved with list / cautious oversized / cold helpful
shape); cold unknown + nearby alternative flagged; comfort unknown without
nearby alternative flagged; comfort unknown WITH nearby alternative allowed;
post-stale repair marker is not a contradiction.

Validation:

```bash
python -m py_compile core/capabilities/contracts.py
pytest tests/test_0201_stage7_18_capability_contracts.py -q
pytest -q
```

Result: targeted `14 passed`; full suite `596 passed, 2 xfailed`
(baseline before 0.20.1.15-18 was 537). Stage 1 integration snapshot — green.

0.20.1.x branch closure: with these four stages the Horizon capability work
is observable, broadly scenario-covered, coefficient-pinned, and contract-checked.
Next direction is to revisit unfinished stages from FUTURE_UPDATES /
OFFLINE_COGNITION_UPDATES (Stages 10, 18, 19) or to begin 0.20.3.x Social
Perception & Attachment Vulnerability roadmap (version line swapped:
0.20.3.x is now the social perception layer; TTS/STT moves to 0.20.4.x).

## 0.20.1.18a — registry detection gap closure

The two detection gaps tracked as `pytest.xfail` after 0.20.1.16 are now closed.

`core/capabilities/registry.py` patterns:

- `text_edit` adds bare-tone-verb pattern
  `(?:переформулируй|перепиши|отредактируй|поправь)\s+(?:чуть|немного|...)?(?:вежливее|официальнее|мягче|...)`.
  Now matches "переформулируй мягче", "перепиши короче", etc.
- `messenger_reply` adds write-to pattern
  `\bнапиши\s+(?:ему|ей|им)\s+в\s+(?:телеграм|telegram|вк|discord|дискорд)`.
  Now matches "напиши ему в Discord", "напиши ей в телеграм".

`tests/test_0201_stage7_16_expanded_capability_scenarios.py` flips the two
xfails into hard expectations and adds parametrized variants.

Validation: `pytest -q` → `601 passed, 0 xfailed`. No false positives in the
full_mechanics_100 scenario summary (still 0 suspicious / 0 contract warnings).

## Stage 19 (Offline Cognition) — Deterministic Maria reaction estimator

Goal: estimate "how Maria, with current trust/interest/irritation, perceives
this message" without adding another LLM call.

Changes:

- New `core/brain/reaction_estimator.py` with `MariaReaction` dataclass and
  `estimate_maria_reaction(analysis, *, state)` /
  `attach_maria_reaction(analysis, *, state)`. Output:
  `{label, valence, boundary, interest_delta_hint, confidence, reasons}`.
- Branches (deterministic, mutually exclusive, evaluated in priority order):
  - `role_resistance` — assistant_task_request >= 0.7 OR boundary_pressure >= 0.55
  - `uncomfortable_pressure` — flirt signal with low trust/affection
  - `shy_playful` — flirt signal with trust >= 50 AND affection >= 45
  - `hostile_signal` — emotion in {anger, annoyance, contempt} + boundary spike
  - `curious_unknown` — knowledge_gap >= 0.55 with no task pressure
  - `warm_acknowledged` — self_disclosure >= 0.45 with trust >= 35, irritation < 30
  - `neutral` — fallback
- All numeric fields bounded by [-1.0, 1.0]; confidence by [0.0, 1.0];
  `interest_delta_hint` magnitude kept small (≤ 0.3) per "no large scoring deltas".
- `PerceptionStage.run()` now calls `attach_maria_reaction` after `extract_all`
  with `ctx.aff_state` as state. Failures degrade gracefully to `maria_reaction = None`.
- `tools/scenario_summary.py` now exposes `maria_reaction` slot per turn with
  privacy-allowlisted keys (label/valence/boundary/interest_delta_hint/confidence/reasons).
- `ENABLE_REACTION_LLM_FALLBACK = False` — the deterministic path is the only one shipped.

Tests: `tests/test_reaction_estimator.py` (17 tests). Covers tolerance for
None/empty/partial input; pure determinism (no `core.llm.chat` call);
each branch label; bounded magnitudes; confidence gate; PerceptionStage
integration via monkeypatched `extract_all`.

Validation:

```bash
python -m py_compile core/brain/reaction_estimator.py core/engine/stages/perception_stage.py
pytest tests/test_reaction_estimator.py -q
```

Result: `17 passed`. Full suite: `618 passed`.

## Stage 18 (Offline Cognition) — Optional dialogue window retriever

Goal: keep `recent_history = last ~6 messages` short while bringing back a
relevant fragment of older dialogue when the user *returns* to a topic that
left the window.

Changes:

- New `core/memory/dialogue_window_retriever.py` with `maybe_retrieve_dialogue_window`
  and `build_dialogue_window_prompt_block`.
- Topic detection via `extract_topic_mentions` against current message.
- "Return signal" = topic mentioned in current message AND topic NOT in
  recent_history. Continuation (topic still in history) → `None` to keep
  the prompt short.
- Match selection is deterministic: most recent past user message in chat_log
  whose topics intersect with current ones, excluding any idx already in
  recent_history. Position-based "skip last N user-messages" fallback only
  applies when recent_history did not carry idx values.
- Prompt block bounded by `MAX_DIALOGUE_WINDOW_CHARS = 800` (tighter than the
  opinion-miner block of 1200).
- `ENABLE_DIALOGUE_WINDOW_LLM_SELECTOR = False` — LLM-driven selection is
  defined as opt-in only and not implemented in this stage.
- **NOT wired into hot path by default.** PerceptionStage and
  prompt_builder do not call the retriever yet. Test
  `test_perception_stage_does_not_call_dialogue_window_retriever` enforces
  this off-by-default invariant by reading the perception_stage source.

Tests: `tests/test_dialogue_window_retriever.py` (12 tests). Covers empty/noisy
input → None; topic still in history → None (continuation); topic return →
window retrieved; no past match → None; overlap with recent_history → None;
prompt block bounded by max_chars; build helper edge cases; LLM selector
disabled invariant; off-by-default invariant on PerceptionStage source.

Validation:

```bash
python -m py_compile core/memory/dialogue_window_retriever.py
pytest tests/test_dialogue_window_retriever.py -q
```

Result: `12 passed`. Full suite: `630 passed`.

## Stage 10 (FUTURE_UPDATES) — Interest calibration invariants (no code change)

Goal: pin the current interest formula contract in tests.

Validation source: `test_runs/20260510_192703_full_mechanics_gemme3_12b.json`
(100-turn live scenario via gemma3:12b). Interest trajectory 15.0 → 36.4
across 100 mixed-pressure turns. No spikes, no collapses, range 15.0..36.8.
This signal validated that the existing weights produce healthy behavior on
real(ish) data, so **no weights are changed** — only invariants are pinned.

Tests: `tests/test_interest_calibration_invariants.py` (18 tests):

- defaults: `INITIAL_INTEREST = 15.0`, `MIN_INTEREST_FOR_NEW_ACQUAINTANCE = 10.0`;
- config keys must remain (`novelty_weight`, `self_disclosure_weight`,
  `opinion_alignment_weight`, `opinion_disagreement_penalty`, `emptiness_penalty`,
  `repetition_penalty`, `boundary_pressure_penalty`, `assistant_task_penalty`,
  `negative_score_weight`, `session_bonus*`, `long_session_*`,
  `delta_min`, `delta_max`, `min`, `max`);
- empty / filler messages → negative delta and `empty:` reason;
- low-signal non-empty messages → `low_signal:` penalty;
- self-disclosure → positive delta;
- new stored opinion contributes novelty;
- session-length bonus only fires for non-empty messages (no farming via filler);
- boundary_pressure decreases interest;
- polite practical capability request only incurs the tiny 0.12 assistant_task
  factor (not the full penalty);
- pressured takeover hits interest harder than polite small request;
- negative score under practical capability decision uses 0.35 dampener factor;
- delta is bounded by [`delta_min`, `delta_max`] = [-2.0, 2.0];
- interest state is clamped to [`min`, `max`] = [0.0, 100.0];
- mode thresholds: <10 disengaged / <25 low_interest / <50 engaged / >=50 curious;
- interest formula must NOT depend on hidden thought / reflection / raw_thought
  fields (Stage 10 spec: "не использовать hidden thought words as signal");
- reasons list is human-readable `label:+/-X.YY` strings (debuggable).

Validation:

```bash
python -m py_compile core/brain/interest.py
pytest tests/test_interest_calibration_invariants.py -q
```

Result: `18 passed`. Full suite: `648 passed` (was 537 baseline before the
0.20.1.15-18 + 18a + Stages 19/18/10 batch — i.e. **+111 tests**).

## Closure note for the 0.20.1.x branch and skipped FUTURE / OFFLINE stages

After this batch:

- the Horizon capability subsystem is observable (15), broadly scenario-covered
  (16), coefficient-pinned (17), contract-checked (18), and detection-complete
  (18a) for the canonical edge phrases known from 0.20.1.13 calibration;
- `Stage 19` (deterministic reaction estimator) ships and is wired into the
  active path as a non-mutating analysis hint;
- `Stage 18` (dialogue window retriever) ships as an off-by-default module
  with bounded prompt-block output, ready for opt-in once we want continuity
  on long-session topic returns;
- `Stage 10` (interest calibration) is pinned without weight changes because
  the live `full_mechanics_100` run validated the current contract.

Outstanding work explicitly *not* started here: optional prompt builder
deduplication; the larger `0.20.3.x` Social Perception & Attachment
Vulnerability roadmap (see `docs/old_roadmaps/social_perception_roadmap.md`);
TTS/STT (postponed to `0.20.4.x`).

## Version line swap — 0.20.3.x ⇄ 0.20.4.x

The version line for the next two major patches was swapped:

- `0.20.3.x` — **Social Perception & Attachment Vulnerability Layer** (was 0.20.4.x).
  Backend-only roadmap lives in `docs/old_roadmaps/social_perception_roadmap.md`.
  Sub-stages: humor schema → humor effects + rudeness correction → people
  mentions + social store → relationship graph + scene roles → attachment
  vulnerability state → emotional events + recovery + relapse → director
  integration + prompt blocks → debug API + manual validation campaign.
- `0.20.4.x` — **TTS/STT** (was 0.20.3.x). Postponed until the social
  perception layer stabilizes.

Rationale: social perception is a prerequisite for safe attachment behavior,
and it is also the natural extension of the closed Horizon capability work.
TTS/STT is independent of Horizon semantics and can sit behind it.

## Stage 0.20.3.1 — Humor & Teasing Schema

Goal: give Maria a deterministic way to distinguish jokes / teasing /
sarcasm / self-irony / funny gossip from real rudeness, **without adding a
new mandatory LLM call** and without mutating relationship state.

Changes:

- New package `core/brain/humor/`:
  - `schema.py` — `HumorAnalysis` dataclass + `HumorType` / `HumorTarget`
    Literal aliases + `HUMOR_TYPES` / `HUMOR_TARGETS` tuples +
    `normalize_humor_payload(payload)` (clamps numeric fields, falls back to
    `none`/`unknown` on invalid types) + `neutral_humor_analysis()`.
  - `taste.py` — `DEFAULT_HUMOR_TASTE` covering all 11 humor types in
    `[-1.0, 1.0]`. Persona override via `profile_value("HUMOR_TASTE", {})`.
    `taste_match_for_humor(humor_type)` reads with safe fallback.
  - `detector.py` — `detect_humor(text, analysis)` and
    `attach_humor_analysis(text, analysis)`. Priority: extractor-provided
    `analysis["humor"]` > deterministic inference from text + analysis fields
    (emotion, warmth, sincerity, score, boundary_pressure, romantic_signal,
    laughter punctuation). Russian markers cover self-irony, sarcasm, teasing,
    funny gossip, offensive joke, boundary-testing, flirty overlay; gossip
    without a clear third-party referent is downgraded to `light_joke`.
  - `prompt_block.py` — re-export of `build_humor_prompt_block` to match the
    spec's path layout.
  - `__init__.py` — public API surface.
- `core/engine/stages/perception_stage.py` calls `attach_humor_analysis`
  after `extract_all`, before `attach_maria_reaction`. Failures degrade
  gracefully to `analysis["humor"] = None`.
- `tools/scenario_summary.py` adds `humor` slot per turn with privacy-allowlist
  keys (`is_humor`, `confidence`, `humor_type`, `target`, `taste_match`,
  `offensiveness`, `warmth`, `flirtiness`, `boundary_risk`, `gossip_factor`,
  `suggested_reaction`, `reasons`).

Tests: `tests/test_humor_schema.py` (26 tests) — schema completeness, taste
constants for every type, normalization of missing/invalid/clamped payloads,
`self_irony` / `teasing(maria)` / `gossip_funny(third_party)` shapes,
detector priority (extractor-provided wins, invalid downgraded), conservative
inference branches, attach helper, taste fallback for unknown type.

Validation:

```bash
python -m py_compile core/brain/humor/__init__.py core/brain/humor/schema.py \
    core/brain/humor/detector.py core/brain/humor/taste.py
pytest tests/test_humor_schema.py -q
```

Result: `26 passed`.

## Stage 0.20.3.2 — Humor Effects & Rudeness Correction

Goal: plug HumorAnalysis into the rudeness/breakup pipeline so jokes /
self-irony / teasing within boundaries do not accumulate rudeness, while
offensive jokes and boundary-testing keep the original penalty.

Changes:

- New `core/brain/humor/effects.py`:
  - `apply_humor_to_rudeness(base_delta, humor) -> float` — softens rudeness
    using per-type factors (self_irony=0.0, light_joke=0.20, teasing=0.25,
    gossip_funny=0.30, absurd=0.35, flirty_joke=0.30, sarcasm=0.55,
    dark_humor=0.65; offensive_joke / boundary_testing keep 1.0).
    Hard guards: `offensiveness >= 0.75` OR `boundary_risk >= 0.75` always
    keep the original penalty. Teasing(maria) softens only when
    `offensiveness < 0.35` AND `boundary_risk < 0.40`. Funny gossip softens
    only when `offensiveness < 0.55`. Humor never *amplifies* base.
  - `compute_humor_enjoyment(humor, state, romance_state) -> float in [-1.0, 1.0]`
    combining Maria's taste, relationship closeness ((aff+trust)/200 * 0.35),
    irritation penalty (irr/100 * 0.30), and a romance amplifier (capped at
    0.20, only for flirty/teasing/light_joke with `boundary_risk < 0.45`).
    Hard cut to negative when offensiveness/boundary_risk hit hardline
    thresholds — romance bias never overrides boundaries.
  - `build_humor_reaction_hint(humor, enjoyment, state, romance_state)` —
    safe structured payload for director (`is_humor`, `humor_type`, `target`,
    `enjoyment`, `boundary_risk`, `offensiveness`, `flirtiness`,
    `react_with`).
  - `build_humor_prompt_block(humor, enjoyment, *, max_chars=400)` — compact
    `[HUMOR CONTEXT]` block for system prompt; bounded by `max_chars`;
    returns empty string when no humor detected (concat-safe).
- `core/brain/breakup.py` — `calculate_rudeness_delta` now ends with a
  `try: detect_humor → apply_humor_to_rudeness; except: pass` block. The
  legacy `is_joke = warmth>=0.5 and sincerity<0.5 and score>=0`
  + `RUDENESS_JOKE_DAMPENER` shortcut is removed: humor layer reads the same
  signals plus lexical markers and produces a more accurate softening.
- `config.RUDENESS_JOKE_DAMPENER` is marked DEPRECATED in a comment (still
  defined; no other code references it).

Tests: `tests/test_humor_effects.py` (28 tests):

- `apply_humor_to_rudeness`: self-irony never increases rudeness even when
  base is positive (forced ≤ 0); polite teasing(maria) softened to 0.25x;
  teasing with offensiveness ≥ 0.35 keeps penalty; offensive_joke and
  boundary_testing keep penalty; funny gossip with low offensiveness softened;
  cruel gossip (offensiveness ≥ 0.55) keeps penalty; humor without signal
  returns base unchanged; humor never amplifies base across all types.
- `compute_humor_enjoyment`: zero when no humor; self-irony positive baseline;
  closeness increases enjoyment; irritation decreases enjoyment;
  romance amplifies flirty/teasing/light only when `boundary_risk < 0.45`;
  romance does NOT amplify when boundary_risk high; high boundary_risk and
  high offensiveness force enjoyment ≤ -0.3; result bounded in [-1, 1].
- `build_humor_prompt_block`: empty when no humor; contains `[HUMOR CONTEXT]`
  + Type/Target/Suggested reaction lines + 'do not moralize' guard; bounded
  by `max_chars`; never includes raw user text.
- `build_humor_reaction_hint`: safe payload shape; `react_with` is the
  detector's suggestion.
- `breakup.calculate_rudeness_delta` integration: self-irony with insult-marker
  word ("я тупой") accumulates ≤ 0; direct insult ("ты тупая дура") keeps
  heavy penalty (≥ 4.0) — joke softening can't bypass real boundary breach;
  warm teasing ("опять ты что-то путаешь))") softened to < 1.0.

Validation:

```bash
python -m py_compile core/brain/humor/effects.py core/brain/breakup.py
pytest tests/test_humor_effects.py -q
pytest -q
```

Result: targeted `28 passed`; full suite `702 passed` (was 648 baseline,
**+54 tests**).

Behavior preserved:
- breakup mercy gates untouched;
- direct insult to Maria still accumulates rudeness toward breakup;
- cold-stage capability pressure still escalates rudeness via the existing
  Horizon 0.20.1.12 branch — humor correction only softens *down*, not up;
- Maria is not forced to like every joke (`compute_humor_enjoyment` can be
  negative even for non-offensive jokes when irritation is high or
  taste mismatches).

## Stage 0.20.3.3 — People Mentions & Social Store

Goal: let Maria remember concrete third-party people the user talks about
(friends, classmates, colleagues, characters in stories) **with source,
confidence and her own cautious attitude** — without treating gossip as
fact and without copying user attitude 1:1 onto Maria's view.

Changes:

- New package `core/social/`:
  - `schema.py` — `PersonAttitude`, `PersonProfile`, `PersonEvent` dataclasses
    + clamping/normalization helpers `normalize_attitude`,
    `normalize_profile_payload`, `normalize_event_payload`,
    `normalize_mention_payload`. `VALID_MENTION_TYPES` and
    `VALID_RELATION_HINTS` enforce safe defaults on bad input.
  - `resolver.py` — `canonical_name(name)` (NFKC + lowercase + ё→е +
    drop "тот самый"/"та самая" particles), `derive_person_id(name)`
    (`p_<8 hex>` from sha1 of canonical name), `NameResolver` with
    alias / display-name / substring-uniqueness lookup.
  - `store.py` — `SocialStore(base_dir="data/social")` with atomic
    `people.json`, atomic `aliases.json`, append-only crash-safe
    `person_events.jsonl` (same JSONL pattern as `chat_log`). Reuses
    `core/io/json_store` for atomic writes. Provides `put_profile`,
    `upsert_profile` (union of aliases/sources, preserves `first_seen_at`,
    overwrites attitude — caller owns the math), `add_alias`,
    `append_event`, `iter_events`, `recent_events`, `next_event_id`,
    `now_iso`. Tolerant to torn JSONL last line.
  - `extractor.py` — owns the social *policy*:
    - `MAX_ATTITUDE_SHIFT_PER_EVENT = 0.08` — single-event hard cap;
    - `USER_ATTITUDE_DAMPENER = 0.30` — user attitude is dampened before
      flowing into Maria;
    - `CRUELTY_PENALTY = 0.10` — cruel reports pull valence/trust down so
      Maria does not co-sign cruelty even when user reports victimhood;
    - `PROMOTE_CONFIDENCE = 0.55` — minimum confidence to create a new
      named profile (or low confidence + emotional significance);
    - generic unnamed mentions (`temporary_label`) only produce events,
      never profiles;
    - cruel/gossip reports raise `safety_flags["cruelty_reported"]` /
      `safety_flags["gossip"]` instead of silently damaging attitude;
    - `process_people_mentions(analysis, *, store, source, raw_text,
      timestamp)` returns audit `{events, profiles_touched,
      promoted_profiles, skipped}`.
  - `prompt_block.py` — `build_people_prompt_block(analysis, *, store,
    max_people=3, max_chars=480)`. Selects mentioned people first, fills
    with most recently seen profiles. Lines: name (relation); valence,
    trust, optional ⚠ flags, optional summary. Closing line:
    "Use this only as cautious context. Do not assume anything not listed."
    Never inlines `facts` / `notable_events` (those are debug-only).
  - `__init__.py` — public API surface.
- `core/engine/stages/perception_stage.py` calls `process_people_mentions`
  AFTER reaction estimator, only when `analysis["people_mentions"]` is a
  non-empty list. Audit is attached to `analysis["social_audit"]`.
  Failures degrade gracefully to `social_audit = None`. No-mention turns
  do NOT touch `data/social/`.
- `tools/scenario_summary.py` exposes `social_audit` slot per turn with
  a privacy-allowlist of keys (`profiles_touched`, `promoted_profiles`,
  `skipped`). The `events` list is excluded from the summary because
  `raw_text_excerpt` is event-level data only.
- New persistence directory `data/social/` (git-ignored runtime data).

Tests: `tests/test_social_people_store.py` (32 tests):

- schema normalization: missing required fields → None; mention without
  name and without temporary_label → None; numeric clamping; unknown
  relation → "unknown"; profile/event normalize-or-None semantics;
- resolver: canonical name handles ё/йо and "тот самый Х" particles;
  `derive_person_id` is stable for case + ё variants; alias / display /
  substring resolution; returns None when no match;
- store: put/get roundtrip with alias indexing; survives reload via fresh
  store instance; JSONL append + recent_events; tolerates torn last line;
- extractor policy:
  - **named third-party with confidence ≥ 0.55** creates a profile;
  - **generic unnamed** ("один чел на паре") creates only a temporary
    event, no profile, no people in store after processing;
  - **gossip** is stored as event-level `gossip_level`, NEVER as
    `profile.facts`; `safety_flags["gossip"]` raised;
  - **single negative story** has |attitude shift| ≤
    `MAX_ATTITUDE_SHIFT_PER_EVENT` regardless of how strongly the user
    reports it (`user_attitude = -0.9` → Maria valence ≥ -0.08);
  - **Maria attitude ≠ user attitude**: dampener `0.30` + cap `0.08`
    means a single user report with `user_attitude = 0.9` produces
    Maria valence well below 0.27;
  - **aliases resolve** to existing profile (does not create a new one);
  - **cruel gossip** raises `safety_flags["cruelty_reported"]` and pulls
    trust down extra (cruelty penalty), but bounded;
  - **repeat mention** increments `mention_count`;
  - **invalid mention** appears in `audit["skipped"]`;
  - **named low-confidence non-significant** mention falls through to
    temporary event (no profile);
- prompt block: empty when no people; includes mentioned profile; bounded
  by `max_chars`; never leaks `facts` / `notable_events` content;
- PerceptionStage integration: skips social pipeline when no mentions;
  processes mentions when present (verified against monkeypatched
  `extract_all` and a temp `SocialStore`).

Validation:

```bash
python -m py_compile \
  core/social/__init__.py core/social/schema.py core/social/resolver.py \
  core/social/store.py core/social/extractor.py core/social/prompt_block.py \
  core/engine/stages/perception_stage.py tools/scenario_summary.py
pytest tests/test_social_people_store.py -q
pytest -q
```

Result: targeted `32 passed`; full suite `734 passed` (was 702 baseline,
**+32 tests**).

Behavior preserved:
- no new mandatory LLM call;
- `data/` schema additive (new files only);
- existing analyses without `people_mentions` skip the social pipeline
  entirely (no I/O);
- persona text and CORE_PERSONA untouched;
- gossip never automatically becomes Maria's belief — it is recorded as a
  report (event) with `gossip_level` and a profile-level `safety_flags`
  raised, while `profile.facts` stays untouched.

Next: Stage 0.20.3.4 — Relationship Graph & Scene Role Consistency, which
builds on this people store.

## Stage 0.20.3.4 — Relationship Graph & Scene Role Consistency

Goal: prevent character.ai-style drama where any new third-party mention
turns into jealousy / betrayal / romantic conflict. Maria must understand
that:

```text
user ↔ Даня = friend
user ↔ Аня = classmate
Maria ↔ Аня = stranger
Аня ≠ romantic rival by default
```

Changes:

- New `core/social/relationship_graph.py` with `RelationshipGraph(base_dir)`
  storing directed edges in `data/social/relationship_graph.json`
  (atomic JSON via existing `core/io/json_store`). Edges:
  `{from, to, relation, confidence, source, updated_at}`. API: `set_edge`
  (upsert with max-confidence merge), `set_symmetric_edge` (writes both
  directions for symmetric relations like friend / partner / sibling),
  `get_edges_from`, `get_edges_to`, `get_edges_between`, `relation_between`
  (highest-confidence pick), `remove_edges_for_node`. Reserved nodes:
  `NODE_USER = "user"`, `NODE_MARIA = "maria"`. `VALID_RELATIONS` covers 14
  canonical relations; unknown relations fall back to "unknown".
- New `core/social/social_consistency_guard.py` — pure deterministic
  anti-drama policy. Detects six trigger types from user text + analysis:
  `romantic_threat`, `comparison_with_maria`, `replacement_threat`,
  `deliberate_provocation`, `betrayal`, `conflict`, plus a `defused` marker
  for explicit "просто друг" / "ничего такого" framing.
  `is_jealousy_justified(triggers) -> (bool, reason)`: jealousy fires ONLY
  when at least one of the explicit triggers is present (defused does not
  cancel an explicit threat). `evaluate_scene_drama(text, analysis)` builds
  the four scene flags (`romantic_context`, `betrayal_context`,
  `jealousy_context`, `conflict_context`) following the spec rules:
  - mentioning a friend / classmate ≠ romantic rival;
  - mentioning another girl ≠ romance penalty by itself;
  - jealousy needs an actual threat to the connection;
  - "просто друг" defuses bare conflict but never an explicit betrayal.
- New `core/social/scene_roles.py` — `build_scene_roles(text, analysis, *,
  store, graph)` returns the per-turn payload `{scene_participants,
  scene_flags, scene_drama}`. Participants are always
  `[user, maria, *third_parties]` with stable distinct ids — anti-merge
  invariant enforced by a final dedup pass. Third-party participants carry
  `relation_to_user` (graph wins over `profile.relation_to_user`) and
  `relation_to_maria` (default `"stranger"`). Anonymous mentions are NOT
  participants (only events).
- `core/social/prompt_block.py` extended with
  `build_scene_roles_prompt_block(scene_roles, *, max_chars=480)`. Returns
  `""` when no third party AND no escalation flag. Otherwise emits
  `[SCENE ROLE CONTEXT]` listing third parties + an explicit anti-drama
  guard line ("not automatically a romantic rival, do not create jealousy /
  betrayal / romantic conflict unless explicitly provided"). When any
  scene flag IS set (justified escalation) the block names the active
  flags so the prompt builder can react proportionately rather than
  silently softening.
- `core/social/__init__.py` re-exports the new public API
  (`RelationshipGraph`, `RelationshipEdge`, `NODE_USER`, `NODE_MARIA`,
  `VALID_RELATIONS`, `SYMMETRIC_RELATIONS`, `build_scene_roles`,
  `detect_jealousy_triggers`, `evaluate_scene_drama`,
  `is_jealousy_justified`, `build_scene_roles_prompt_block`).
- `core/engine/stages/perception_stage.py` calls `build_scene_roles`
  AFTER social audit. Always runs (even on turns without
  `people_mentions`) so the anti-drama guard can react to text-only
  triggers like "Аня лучше тебя". Failures degrade gracefully to
  `analysis["scene_roles"] = None`.
- `tools/scenario_summary.py` exposes `scene_roles` per turn with a
  privacy-allowlist (`scene_participants`, `scene_flags`, `scene_drama`).

Tests: `tests/test_social_scene_roles.py` (38 tests):

- edge normalization (missing fields → None, unknown relation → "unknown",
  confidence clamped);
- graph: set_edge upserts taking max confidence; symmetric helper writes
  both directions for symmetric relations; non-symmetric relation via
  symmetric helper writes forward only; remove_edges_for_node;
  `relation_between` picks highest-confidence; survives reload;
  `SYMMETRIC_RELATIONS` includes friend/partner, excludes rival;
- anti-drama neutral: parametrized over four canonical neutral phrases
  ("Сегодня Аня помогла мне с лабой", "Я гулял с подругой", "Даня скинул
  мне код", "Встретил одногруппника, обсудили дз") — all four scene
  flags must stay False;
- explicit triggers MUST escalate: "встречаюсь с Аней" (romantic
  context), "Аня лучше тебя" (comparison + jealousy + romantic),
  "заменю тебя на нормальную" (replacement + jealousy + romantic),
  "ну приревнуй меня)" (provocation + jealousy), "он мне изменил с Аней"
  (betrayal + jealousy);
- conflict marker triggers conflict only, not jealousy/romantic;
- "просто подруга, ничего такого" defuser drops bare conflict;
- defuser does NOT cancel explicit betrayal;
- `is_jealousy_justified` returns the trigger reason for justified
  cases and "no_trigger_present" otherwise;
- scene roles anti-merge: user / maria / third_party are 3 distinct ids;
- unresolved mention → not in participants (only event in store);
- anonymous mention → not in participants;
- graph relation wins over `profile.relation_to_user`;
- default relation_to_maria is "stranger";
- neutral mention → all four scene flags False (no unjustified betrayal);
- prompt block empty when no third party AND no flags;
- neutral third party → anti-drama guidance lines present;
- escalation → active flags named in block;
- block bounded by max_chars;
- PerceptionStage integration: `scene_roles` attached even without
  `people_mentions`; flags False on plain message.

Validation:

```bash
python -m py_compile \
  core/social/relationship_graph.py core/social/scene_roles.py \
  core/social/social_consistency_guard.py core/social/prompt_block.py \
  core/social/__init__.py core/engine/stages/perception_stage.py \
  tools/scenario_summary.py
pytest tests/test_social_scene_roles.py -q
pytest -q
```

Result: targeted `38 passed`; full suite `772 passed` (was 734 baseline,
**+38 tests**).

Behavior preserved:
- no new mandatory LLM call;
- romance system NOT changed;
- breakup gates NOT changed;
- jealousy is NOT disabled — it just requires an actual trigger;
- persona text and CORE_PERSONA untouched;
- existing analyses without `people_mentions` and without trigger phrases
  still produce a scene_roles payload (with default user/maria pair and
  all flags False) so downstream consumers can rely on the slot existing.

Next: Stage 0.20.3.5 — Attachment Vulnerability / Emotional Health State,
which builds on graph + scene_roles to model long-term emotional safety.

## Stage 0.20.3.9 — Adaptive Shared Humor / "С кем поведёшься"

Goal: let Maria slowly adapt part of her humor recognition toward the
user's safe recurring humor patterns when the relationship is closed
enough — without ever turning Maria into a copy of the user and without
normalizing harmful jokes.

Changes:

- New `core/brain/humor/adaptive_taste.py`:
  - `HumorTasteState` dataclass with four taste maps (`base_taste`,
    `learned_taste`, `shared_taste`, `rejected_taste`), `user_alignment`,
    counters (`shared_humor_events`, `successful_humor_events`,
    `boundary_crossing_humor_events`), and timestamps
    (`last_shift_at`, `last_shared_joke_at`).
  - `ADAPTABLE_TASTE_KEYS` (8 safe types) and `REJECTED_TASTE_KEYS`
    (adaptable + offensive_joke + boundary_testing). Unknown keys are
    dropped silently by `normalize_taste_state` so a corrupt file never
    poisons downstream consumers.
  - Atomic JSON persistence via existing `core/io/json_store`:
    `data/humor_taste_state.json`. Append-only `data/humor_events.jsonl`
    for compact events.
- New `core/brain/humor/drift.py`:
  - `DialogueStats(total_messages, shared_humor_events)` snapshot.
  - `adaptive_humor_enabled(state, romance_state, dialogue_stats)` —
    spec gate `affection ≥ 70`, `trust ≥ 60`, `romance ≥ 25`,
    `total_messages ≥ 300`, `shared_humor_events ≥ 20`.
  - `compute_closeness_factor(state, romance_state) → [0,1]`
    `= clamp((aff + trust + romance) / 300, 0, 1)`.
  - `compute_humor_drift(humor, state, romance_state, taste_state, stats)`
    — pure function, returns deltas dict or empty. Two branches:
    - **boundary** (offensiveness > 0.55 OR boundary_risk > 0.45):
      always produces ONLY `rejected_taste` increment, regardless of gate;
    - **safe positive** (only fires when gate is open AND humor type is
      in `SAFE_DRIFT_HUMOR_TYPES = {absurd, self_irony, teasing,
      flirty_joke, sarcasm}`): produces tiny `learned_taste`
      (≤ 0.015), `shared_taste` (half of learned), `user_alignment`
      (quarter of learned).
  - `apply_drift(state, drift, *, timestamp)` — clamps per axis to
    declared bounds (`learned [-1, 1]`, `shared [0, 1]`,
    `rejected [0, 1]`, `alignment [0, 1]`); increments counters via
    `successful_event` / `boundary_event` flags; updates timestamps.
- New `core/brain/humor/shared_humor.py`:
  - `record_humor_event(humor, drift, *, source, timestamp, store_path)`
    — appends a compact JSONL line (only humor_type, target,
    safe_for_adaptation, warmth, offensiveness, boundary_risk,
    maria_enjoyment, drift_applied, source). Mirrors the crash-safe
    append pattern from `chat_log` and `core/social/store`.
  - `process_humor_drift(humor, state, romance_state, *, dialogue_stats,
    source, timestamp, taste_path, events_path)` — single end-to-end
    entry: load taste, compute drift, apply, persist, append event.
    Returns audit `{drift, applied, successful_event, boundary_event,
    event_recorded}`.
  - `build_shared_humor_prompt_block(taste_state, *, max_chars=700)` —
    bounded `[SHARED HUMOR CONTEXT]` block. Returns "" when no shared
    humor signal yet (no shared_taste AND alignment < 0.05 AND
    shared_humor_events < 5). Surfaces only humor types and qualitative
    levels (low / low-positive / medium / high) — never raw deltas,
    never timestamps, never raw user text.
- `core/brain/humor/__init__.py` re-exports the new public API.
- `core/engine/stages/persistence_stage.py` adds step 12.5: after state
  is persisted, if `analysis["humor"]` is humor, call
  `process_humor_drift` with current state + romance_state +
  `DialogueStats(total_messages=message_counter, shared_humor_events=
   taste_for_stats.shared_humor_events)`. Audit attached to
  `analysis["humor_drift_audit"]`. Wrapped in `try/except` — failures
  never break persistence.
- `tools/scenario_summary.py` exposes `humor_drift_audit` per turn with
  privacy-allowlist (`drift, applied, successful_event, boundary_event,
  event_recorded`).

Tests: `tests/test_adaptive_humor_taste.py` (32) +
`tests/test_shared_humor_prompt_block.py` (8) = **40 tests**.

Coverage:
- normalization tolerates garbage payload, drops unknown keys, clamps
  out-of-range numerics;
- atomic JSON roundtrip preserves all four taste maps + counters;
- gate disabled below each of the five thresholds; enabled exactly at
  thresholds; tolerant to None inputs;
- `compute_humor_drift`: empty for None / no-humor / low-confidence;
  empty when gate closed; empty for unsafe humor types
  (`dark_humor` etc); parametrized over all five safe types — each
  produces tiny bounded deltas; high offensiveness blocks positive
  drift and writes `rejected_taste`; high boundary_risk blocks
  positive drift and writes `rejected_taste`;
- `apply_drift` clamps per axis bounds; ignores unknown keys;
  increments counters; updates timestamps;
- repeated safe humor accumulates `shared_humor_events` (3 events →
  counter = 3, learned/shared > 0);
- `process_humor_drift` end-to-end writes state + appends event line;
  `safe_for_adaptation: true` for safe drift; `boundary_event: true`
  for offensive_joke; empty audit for non-humor input;
- recorded JSONL line never includes private fields (`user_text`,
  `raw_text*`, `thought`, `raw_thought`, `system_prompt`, `memories`,
  `facts`);
- shared humor never bypasses real insult: `apply_humor_to_rudeness`
  for `offensive_joke` keeps base regardless of taste history;
- prompt block: empty when no signal; empty when only tiny alignment;
  contains shared patterns + qualitative levels; falls back to
  learned_taste when shared empty; includes safety guidance ("never
  overrides boundaries"); bounded by `max_chars`; does NOT leak raw
  numeric deltas, timestamps, or counter names; accepts both
  `HumorTasteState` and dict payload.

Validation:

```bash
python -m py_compile \
  core/brain/humor/adaptive_taste.py core/brain/humor/drift.py \
  core/brain/humor/shared_humor.py core/brain/humor/__init__.py \
  core/engine/stages/persistence_stage.py tools/scenario_summary.py

pytest tests/test_adaptive_humor_taste.py tests/test_shared_humor_prompt_block.py -q
pytest -q
```

Result: targeted `40 passed`; full suite `812 passed` (was 772 baseline,
**+40 tests**).

Behavior preserved:
- no new mandatory LLM call;
- `data/` schema additive (two new files appearing on first humor turn);
- persona text and CORE_PERSONA untouched;
- breakup / rudeness / boundary guards untouched — `apply_humor_to_rudeness`
  is the same function as in 0.20.3.2; adaptive layer never feeds back
  into rudeness math;
- gate uses high thresholds (aff ≥ 70, trust ≥ 60, romance ≥ 25,
  total_messages ≥ 300, shared_humor_events ≥ 20) so no drift in early
  acquaintance;
- single drift step is tiny (≤ 0.015) and shared step is half of that;
  even at full closeness it takes many safe humor turns to move
  `shared_taste` meaningfully;
- recurring offensive jokes accumulate in `rejected_taste` and the gate
  can read it later — they never silently become "shared".

## Stage 0.20.3.5 + 0.20.3.6 — Attachment Vulnerability + Emotional Events

Goal: introduce a gated emotional-health layer (the "cost of closeness")
that reacts to meaningful relationship events — absence, cold replies
after Maria's interest, comparison with another girl, replacement
threats, reassurance, apology, quality time — without becoming a
clinical illness simulation and without enabling guilt-tripping.

Combined into one stable batch because Stage 5 alone (state with no
consumers) would be dead code waiting for Stage 6 detectors and
recovery/relapse logic.

Changes:

- New package `core/brain/emotional_health/`:
  - `schema.py` — `EmotionalHealthState` (stability/loneliness/insecurity/
    hurt/recovery_debt/relapse_sensitivity + 2 timestamps), per-axis
    bounds, `safe_default_state`, backward-compatible `normalize_state`,
    atomic JSON persistence (`data/emotional_health.json`),
    `emotional_vulnerability_enabled(state, romance_state)` gate
    (affection ≥ 60, trust ≥ 50, romance ≥ 20).
    `EmotionalEvent` dataclass + 11 `EMOTIONAL_EVENT_TYPES`
    (ABSENCE / COLD_REPLY / RUDE_REPLY / IGNORED_INTEREST /
    ROMANTIC_RIVAL_MENTION / COMPARISON_WITH_OTHER_GIRL /
    REPLACEMENT_THREAT / REASSURANCE / AFFECTIONATE_ATTENTION /
    APOLOGY / QUALITY_TIME) with `NEGATIVE_EVENT_TYPES` /
    `POSITIVE_EVENT_TYPES` sets.
  - `events.py` — append-only crash-safe JSONL log
    (`data/emotional_health_events.jsonl`) with
    `append_event` / `iter_events` / `recent_events` /
    `recent_events_of_type(within_days=14)` for relapse detection.
  - `detector.py` — `detect_events(text, analysis, *, eh_state,
    prior_assistant_text, now)` reads `analysis["scene_roles"]
    ["scene_drama"]["triggers"]` from Stage 0.20.3.4 plus extractor
    fields (emotion / score / sincerity / boundary_pressure /
    reconciliation / directed_at_mari) plus Russian markers
    (REASSURANCE / APOLOGY / AFFECTIONATE / QUALITY_TIME). Conservative
    rules:
      - **ABSENCE** only when `last_interaction_at` exists AND gap ≥ 24h;
        intensity scales linearly to 1.0 at 7-day silence.
      - **COMPARISON_WITH_OTHER_GIRL** / **REPLACEMENT_THREAT** /
        **ROMANTIC_RIVAL_MENTION** flow from `scene_drama.triggers`
        (no false positive on neutral mentions like "Аня помогла с лабой").
      - **ROMANTIC_RIVAL_MENTION** requires a third-party participant
        actually present in the scene.
      - **RUDE_REPLY** only when emotion ∈ {annoyance, anger, contempt}
        AND `directed_at_mari` AND `sincerity ≥ 0.5` AND `score < 0`.
      - **COLD_REPLY** / **IGNORED_INTEREST** ONLY when the prior
        Maria turn carried a personal-interest marker AND the user
        replied short / dismissively (no excuse marker like "занят /
        потом расскажу").
      - **REASSURANCE** / **APOLOGY** / **AFFECTIONATE** / **QUALITY_TIME**
        from sincerity-gated marker lists + reconciliation field.
  - `update.py` — `apply_negative_event(state, event, *,
    relapse_multiplier)` with per-event base deltas (REPLACEMENT_THREAT
    is the heaviest; ABSENCE only loneliness + small stability hit;
    intensity scales linearly). `compute_relapse_multiplier(event_type,
    *, recent_events, now, window_days=14)` returns 1.0 .. 2.0 based on
    same-type negative events in the window (`+0.25` per occurrence,
    capped). `loneliness` is **never** amplified by relapse — long
    absence is its own signal.
  - `recovery.py` — `apply_positive_event(state, event)` with per-event
    healing tables (REASSURANCE focuses on insecurity, APOLOGY focuses
    on hurt, AFFECTIONATE/QUALITY_TIME reduce loneliness, all four raise
    stability). Recovery_debt rises by 30% of the magnitude healed —
    "часть следа остаётся" so a comforted wound primes a relapse.
    Relapse_sensitivity falls by 0.05 per positive event.
  - `prompt_block.py` — `build_attachment_prompt_block(eh_state, *,
    state, romance_state, max_chars=700, require_gate=True)` returns
    a bounded `[ATTACHMENT VULNERABILITY CONTEXT]` block. Empty when
    gate closed OR no axis is meaningfully elevated. Always includes
    the **anti-manipulation guard list**: forbidden phrases ("ты обязан
    писать мне каждый день", "если ты уйдёшь, я сломаюсь",
    "не общайся с другими девушками", "выбирай: я или они",
    "ты виноват, что мне плохо") and allowed phrases ("мне стало
    неприятно", "я немного ревную", "мне хочется больше внимания",
    "после такого я стала осторожнее", "я не запрещаю тебе общаться,
    но сравнивать меня с ней было больно") plus the closing line
    "Do not weaponize her vulnerability against the user."
  - `__init__.py` exposes `process_emotional_events(user_input,
    analysis, *, state, romance_state, prior_assistant_text, now,
    eh_path, events_path)` — single end-to-end entry: detect, gate,
    apply (with relapse multiplier from history), persist, append
    event log. Always returns `{enabled, events, skipped, deltas,
    state_after}` audit. Gate-closed turns still bump
    `last_interaction_at` so absence math stays correct.
- `core/engine/stages/persistence_stage.py` step 12.6 calls
  `process_emotional_events(...)` after state is persisted. Picks the
  PRIOR assistant message from `history` (skips the just-appended
  current reply). Audit attached to `analysis["emotional_health_audit"]`.
  Wrapped in `try/except` — never blocks persistence on failure.
- `tools/scenario_summary.py` exposes `emotional_health_audit` per turn
  with privacy-allowlist `enabled / events / skipped / deltas /
  state_after`.

Tests: 44 across three files.

`tests/test_emotional_health_state.py` (16):
- safe defaults; garbage payload → safe state; out-of-range values
  clamped per axis; backward-compatible partial payload; gate disabled
  below each of the three thresholds; gate enabled at thresholds;
  gate tolerant to None; atomic JSON roundtrip preserves all axes +
  timestamps; missing file returns safe default; full event taxonomy;
  negative/positive sets disjoint.

`tests/test_emotional_events.py` (16):
- neutral third-party mention produces no comparison/replacement event;
- short reply WITHOUT prior interest is not COLD_REPLY (anti-spec
  invariant);
- comparison/replacement create their respective events;
- replacement hurt > comparison hurt in apply delta;
- ROMANTIC_RIVAL_MENTION only when a third party is in scene;
- COLD_REPLY after personal-interest question; skipped when user
  explains ("занят сейчас, потом расскажу");
- RUDE_REPLY for sincere directed annoyance;
- ABSENCE: under 24h → no event, over 24h → event, intensity grows
  with silence length;
- prompt block: anti-manipulation guard present (5 forbidden + 3
  allowed phrases + weaponize line); empty when gate closed; empty
  when state healthy;
- `process_emotional_events`: gate-closed events recorded in `skipped`
  and not applied; gate-open events applied and persisted, state_after
  shows insecurity > 0 and stability < 100.

`tests/test_emotional_recovery_relapse.py` (12):
- REASSURANCE reduces insecurity + hurt, raises stability, raises
  recovery_debt, sets `last_reassurance_at`;
- APOLOGY focuses on hurt only (no loneliness change);
- recovery_debt > 0 after any positive event;
- positive event lowers relapse_sensitivity;
- `compute_relapse_multiplier`: 1.0 with no history; 1.0 for positive
  events; grows with recent repeats; capped at 2.0; ignores events
  beyond the 14-day window;
- spec relapse example end-to-end: первый сравнил → обиделась →
  успокоил → recovery_debt > 0 → через неделю снова сравнил →
  больнее (`abs(second_delta.hurt) > abs(first_delta.hurt)`,
  `abs(second_delta.insecurity) > abs(first_delta.insecurity)`);
- loneliness NOT amplified by relapse multiplier (other axes ARE);
- bounds invariant: even max event + max relapse keeps every axis
  in [0, 100];
- negative branch ignores positive events and vice versa.

Validation:

```bash
python -m py_compile \
  core/brain/emotional_health/__init__.py core/brain/emotional_health/schema.py \
  core/brain/emotional_health/detector.py core/brain/emotional_health/update.py \
  core/brain/emotional_health/recovery.py core/brain/emotional_health/events.py \
  core/brain/emotional_health/prompt_block.py \
  core/engine/stages/persistence_stage.py tools/scenario_summary.py

pytest tests/test_emotional_health_state.py tests/test_emotional_events.py \
       tests/test_emotional_recovery_relapse.py -q
pytest -q
```

Result: targeted `44 passed`; full suite `856 passed` (was 812 baseline,
**+44 tests**).

Behavior preserved:
- no new mandatory LLM call;
- `data/` schema additive (two new files appearing on first turn);
- persona text and CORE_PERSONA untouched;
- breakup / mercy / rudeness / romance gates untouched — emotional
  health is its own state, NEVER feeds back into them;
- module is named `emotional_health`, NOT `mental_illness` (per spec);
- all detector branches are conservative — neutral mentions, short
  replies without prior interest, and absences under 24h produce ZERO
  events;
- gate uses high thresholds (aff ≥ 60, trust ≥ 50, romance ≥ 20) so
  early acquaintance never triggers attachment math even if the
  detector finds events (recorded in `skipped` only);
- prompt block is empty unless the gate is open AND there is a real
  signal — keeps the prompt budget clean for ordinary turns;
- recovery_debt residue + relapse multiplier together model the spec
  example (comforted wound + later repeat → larger hit) without making
  one bad turn collapse the relationship.

Next: Stage 0.20.3.7 — Director Integration & Prompt Blocks (wire
`[PEOPLE CONTEXT]`, `[SCENE ROLE CONTEXT]`, `[HUMOR CONTEXT]`,
`[SHARED HUMOR CONTEXT]`, `[ATTACHMENT VULNERABILITY CONTEXT]` into the
real prompt builder under explicit budget control) and Stage 0.20.3.8 —
Debug API & manual test campaign.

## Stage 0.20.3.7 + 0.20.3.8 — Director Integration & Debug API (combined)

Goal: wire every prompt block accumulated in 0.20.3.1 → 0.20.3.6 + 0.20.3.9
into the real `prompt_builder` under bounded budget control, expose
advisory director hints, add backend-only debug endpoints with sanitized
serializers, and a single feature-flags registry.

Combined into one batch — both stages are pure integration, no behavior
change in extractor / breakup / mood math.

Changes:

- New `core/engine/feature_flags.py` — `DEFAULT_FEATURE_FLAGS` (8 keys:
  `humor_layer`, `humor_drift`, `social_people_memory`,
  `relationship_graph`, `scene_role_consistency`,
  `attachment_vulnerability`, `social_prompt_blocks`,
  `social_director_hints`). All default to True. Persona override via
  `profile_value("FEATURE_FLAGS", {})`. Env override via
  `MARI_DISABLE_<FLAG>=1`. Pure function `get_flags()` recomputes on
  each call — no module-level cache.
- New `core/engine/social_prompt_blocks.py`:
  - `build_social_perception_section(analysis, *, relationship_state,
    romance_state, max_chars=2400)` — combines five sub-blocks
    (`[HUMOR CONTEXT]`, `[SHARED HUMOR CONTEXT]`, `[PEOPLE CONTEXT]`,
    `[SCENE ROLE CONTEXT]`, `[ATTACHMENT VULNERABILITY CONTEXT]`) under
    a single `# СОЦИАЛЬНЫЙ КОНТЕКСТ` section. Each sub-block respects
    its own `max_chars` and the section respects the global cap. Returns
    `""` when nothing useful (concat-safe). Honors feature flags.
  - `compute_social_director_hints(analysis, *, eh_state,
    relationship_state, romance_state)` returns a small advisory list
    of labels from `ALLOWED_DIRECTOR_HINTS = (laugh_softly,
    suppressed_smile, deadpan_teasing, mock_annoyed,
    ask_clarification_about_person, set_boundary, show_mild_jealousy,
    show_hurt, withdraw_warmth, reassured_softening)`. Pure
    deterministic. Honors `MARI_DISABLE_SOCIAL_DIRECTOR_HINTS`. Dedup'd.
- `core/engine/prompt_builder.py` `build_system_prompt` now appends two
  optional sections at the end: the combined social context and a tiny
  `# РЕЖИССЁР: ПОДСКАЗКИ` line listing hint labels. Both wrapped in
  `try/except` — failures degrade silently. Existing post-processing,
  greeting bans, technical rules, persona/profile blocks, voice rules
  are untouched.
- New sanitized serializers:
  - `core/social/debug_serializers.py` — `serialize_profile`,
    `serialize_profiles`, `serialize_event`, `serialize_recent_events`,
    `serialize_scene`. Allow-listed fields per object. **Drops** facts /
    notable_events / traits (may carry user-provided text); drops
    `raw_text_excerpt` from events.
  - `core/brain/emotional_health/debug_serializers.py` — `serialize_state`,
    `serialize_event(s)`. Allow-listed.
- `apps/webui_debug_api.py` adds 10 new endpoints under `/api/webui/`:
  - `GET  /feature_flags`             → `{defaults, effective}`
  - `GET  /social/people`             → list of sanitized profiles
  - `GET  /social/person/<id>`        → one sanitized profile (404 if missing)
  - `GET  /social/events?limit=N`     → recent sanitized events
  - `GET  /social/scene/last`         → `scene_roles` from runtime.last_analysis
  - `GET  /emotional_health/state`    → sanitized EH state
  - `GET  /emotional_health/events?limit=N` → sanitized recent events
  - `POST /emotional_health/recover`  → manually apply REASSURANCE /
                                        APOLOGY / AFFECTIONATE_ATTENTION /
                                        QUALITY_TIME (validates type,
                                        clamps intensity, returns
                                        `{applied, delta, state_after}`)
  - `GET  /humor/last`                → last `analysis["humor"]` (allow-listed)
  - `GET  /humor/taste`               → current adaptive taste state

Tests: 34 across two new files.

`tests/test_social_prompt_blocks.py` (20):
- feature flags: required keys present, default = True; env disable works;
- combined section empty when no signals; section disabled when master flag off;
- humor block fires when analysis has humor; scene block emits anti-drama line for
  neutral third party; emotional-health block emits when signal elevated;
- combined section bounded under `max_chars` even with all blocks active;
- director hints: only allowed labels returned; `set_boundary` for comparison;
  `set_boundary` for offensive humor; `show_mild_jealousy` for jealousy_context
  without betrayal; `show_hurt + withdraw_warmth` for betrayal_context;
  neutral third party → no jealousy hint, instead `ask_clarification_about_person`;
  `show_hurt` when EH `hurt >= 30` or `loneliness >= 35`;
  `reassured_softening` when `emotional_health_audit` has REASSURANCE event;
  high humor enjoyment → `laugh_softly` / `suppressed_smile`;
  hints disabled when env flag off; dedup ensures one label per hint type.

`tests/test_social_debug_api.py` (14):
- feature_flags endpoint returns defaults + effective;
- social/people empty initially; person/<id> returns 404 for unknown;
- profile serializer drops `facts` and `notable_events` (PRIVATE FACT /
  PRIVATE EVENT planted in fixture, asserted absent in JSON blob);
- social/events validates `limit`;
- social/scene/last returns dict;
- emotional_health/state returns safe default; events list empty;
- recover endpoint validates `event_type` (returns 400 with `allowed` list
  when unknown type); applies REASSURANCE end-to-end (state_after shows
  hurt ↓, insecurity ↓, stability ↑); clamps `intensity` past 1.0;
- humor/last and humor/taste return expected shapes;
- privacy regression: across all 8 GET endpoints, no `raw_text_excerpt`,
  `system_prompt`, `raw_thought`, `hidden_thought`, `PRIVATE FACT`,
  `PRIVATE EVENT` substring appears in any JSON response.

Validation:

```bash
python -m py_compile \
  core/engine/feature_flags.py core/engine/social_prompt_blocks.py \
  core/engine/prompt_builder.py apps/webui_debug_api.py \
  core/social/debug_serializers.py \
  core/brain/emotional_health/debug_serializers.py

pytest tests/test_social_prompt_blocks.py tests/test_social_debug_api.py -q
pytest -q
```

Result: targeted `34 passed`; full suite `890 passed` (was 856 baseline,
**+34 tests**).

Behavior preserved:
- no new mandatory LLM call;
- main prompt semantics, technical rules, voice rules, persona block,
  greeting bans, gender-fix post-processing — unchanged;
- director state machine (`build_director_context`) untouched — hints
  are a separate advisory channel appended by prompt_builder;
- breakup / rudeness / mood / romance / extractor — untouched;
- every new endpoint exposes only allow-listed fields; raw text /
  thoughts / system prompts cannot leak through the debug surface;
- empty-response invariant: every block returns `""` when nothing
  meaningful to add, so ordinary turns pay zero prompt-budget cost.

0.20.3.x branch closure:
```
0.20.3.1 ✅ Humor schema
0.20.3.2 ✅ Humor effects & rudeness correction
0.20.3.3 ✅ People mentions & social store
0.20.3.4 ✅ Relationship graph & scene roles
0.20.3.5 ✅ Attachment vulnerability state
0.20.3.6 ✅ Emotional events / recovery / relapse
0.20.3.7 ✅ Director integration & prompt blocks
0.20.3.8 ✅ Debug API & manual test campaign
0.20.3.9 ✅ Adaptive shared humor
```

The 0.20.3.x Social Perception & Attachment Vulnerability roadmap is
fully implemented.

## 0.20.3.10 — Runtime Architecture Hardening / Scalability Foundation

Goal: prepare the backend for the upcoming `0.20.4.x` Voice / TTS / STT
branch and future engine extraction by introducing manager-layer
contracts that sit ON TOP of the existing pipeline rather than replacing
it. **No behavior change.** All managers are composition-owned by
`MariaBrain` (NOT subclasses).

This is the final patch in the `0.20.3.x` Social Perception &
Attachment Vulnerability branch.

Changes:

- New `core/engine/subsystems.py` — `BrainSubsystem` base/protocol with
  `subsystem_name` + `debug_state()`. Used by every new manager.
- New `core/runtime/` package:
  - `modifications.py` — `StateDelta`, `SideEffect`, `ModificationPlan`,
    `merge_deltas`, `apply_clamps`. Bounded *proposals* of state
    changes; never write files; deterministic; gated dominates on merge.
  - `events.py` — `RuntimeEvent`, `make_event`, `record_runtime_event`,
    `recent_runtime_events`, `set_runtime_event_sink`,
    `clear_runtime_events`. In-process ring buffer (200 events).
    Recursive payload sanitizer drops `raw_thought`, `thought`,
    `system_prompt`, `raw_text*`, `memories`, `facts`, `api_key`, `secret`,
    `Authorization`, `password`, `token`. Sink failures swallowed.
- New `core/perception/` package:
  - `statement.py` — `StatementContext`, `ParsedStatement`,
    `StatementResult`. `ParsedStatement` carries raw / normalized text,
    text features, full extractor analysis (compat), and structured
    slices for `requested_capability`, `topic_mentions`, `humor`,
    `people_mentions`, `scene_roles`, `emotional_event_candidates`,
    `maria_reaction`.
  - `parser.py` — `StatementParser.parse(text, context, *,
    pre_computed_analysis=None)`. Two paths: (a) reuse pre-computed
    analysis (slices it); (b) run deterministic helpers (registry +
    humor + topic_mentions + reaction estimator) WITHOUT calling LLM.
  - `modifier.py` — `StatementModifier.build_plan(parsed, context)`.
    Conservative deterministic rules mirror accepted behavior: capability
    pressure → small irritation proposal; self-irony → zero rudeness
    proposal; comparison/replacement/jealousy → bounded hurt proposals
    GATED by `emotional_vulnerability_enabled`; ABSENCE → loneliness
    proposal (gated); safe humor → `humor_drift_hint` SideEffect.
    Plan is informational — `PersistenceStage` remains the single
    writer.
  - `manager.py` — `StatementManager.process(text, context, *,
    pre_computed_analysis=None) -> StatementResult`. Composition.
- New `core/capabilities/permissions.py` — `CapabilityPermission` +
  `DEFAULT_CAPABILITY_PERMISSIONS` (planning/explain/summarize=direct;
  code_help/study_help/write_message/text_edit=limited;
  create_document=nearby_alternative; messenger_reply=draft_only;
  web_search=disabled). `VALID_MODES = direct / limited / draft_only /
  nearby_alternative / disabled`. Optional override via
  `config.CAPABILITY_PERMISSIONS`.
- New `core/capabilities/manager.py` — `CapabilityManager` +
  `CapabilityResolution`. `resolve(name)` / `resolve_from_text(text)` /
  `resolve_from_analysis(analysis)`. Fuses registry `enabled` with
  permission `enabled` + permission `mode`. Unknown capability →
  safe `disabled`. Owns *availability* only; existing
  `evaluate_capability_policy` / `build_capability_handler_guidance`
  remain authoritative for *willingness*.
- New `core/capabilities/boundary_rules.py` — read-only
  `CAPABILITY_BOUNDARY_RULES` table for cold/reserved/cautious/comfort/
  close/very_close/irritated/shutdown stages. Mirrors the constants
  encoded in `relationship_profiles.py`. Comfort cap is mood-sensitive
  in the resolver; the table records the neutral default.
- New `core/engine/director_manager.py` — `DirectorManager` +
  `DirectorInput` + `DirectorDecision`. Composition wrapper around
  `build_director_context` and the Stage 0.20.3.7
  `compute_social_director_hints`. Accepts a `CapabilityResolution`
  and injects it into the analysis copy used by the underlying director.
  Never re-parses raw text. Never mutates state.
- New `core/engine/response_manager.py` — `ResponseManager` +
  `ResponseInput` + `ResponseResult`. Composition wrapper around
  `ResponseStage`. Preserves the established ordering: LLM reply →
  first capability_reply_repair → stale guard → final
  relationship-aware repair pass → postprocess. Returns typed
  `ResponseResult(reply_text, raw_reply_text, repaired,
  repair_reasons[], postprocess_debug, analysis_updates)`.
- New `core/memory/manager.py` — `MemoryManager` minimal facade.
  `build_context()` returns sanitized snapshot (people count, EH state,
  taste-state summary, vector-memory presence). `build_update_plan(...)`
  returns `[]` — actual writes still live in PersistenceStage.
- `config.py` extended with **dedicated extractor model profile**:
  `EXTRACTOR_MODEL = None` (None → fall back to MODEL),
  `EXTRACTOR_MODEL_ENABLED = True`, `EXTRACTOR_TEMPERATURE = 0.0`,
  `EXTRACTOR_TIMEOUT_SECONDS = 20`, `EXTRACTOR_NUM_CTX = 4096`. Plus
  fully-typed `LLM_PROFILES = {"response": ..., "extractor": ...}`.
- `core/llm.py` extended (existing `chat()` / `chat_strict()` UNCHANGED):
  - `resolve_llm_profile("response" | "extractor")` returns the effective
    profile dict. **Live `EXTRACTOR_MODEL` config wins** over the
    static `LLM_PROFILES` snapshot so persona / tests can override at
    runtime. Falls back to `MODEL` when `EXTRACTOR_MODEL` is None or
    `EXTRACTOR_MODEL_ENABLED = False`.
  - `chat_with_profile(messages, profile_name, *, max_tokens,
    temperature_override)` routes through the resolver. When the resolved
    model equals `MODEL`, delegates to existing `chat()` to keep cache /
    usage-stats consistent. Otherwise issues a direct `requests.post`
    with profile-specific options.
- `core/engine/brain.py`: `MariaBrain.__init__` adds five
  composition-owned manager attributes (`statement`, `capabilities`,
  `memory_manager`, `director_manager`, `response_manager`) + a
  `subsystems` property returning the manager dict for debug. Existing
  pipeline stages (`brain.memory`, `brain.director`, `brain.response`,
  `brain.perception`, `brain.opinion`, `brain.relationship`,
  `brain.persistence`) **remain pipeline stages** for backward
  compatibility — `tests/test_brain_facade.py` still passes unchanged.

Tests: 89 new across 9 files.

| File | Count | What it pins |
|---|---|---|
| `tests/test_brain_subsystem_base.py` | 7 | composition (no MariaBrain inheritance), subsystems map, debug_state shape, legacy stage attributes preserved |
| `tests/test_statement_modifier.py` | 13 | empty plan safe; deltas merge by (target, field); max_abs_delta + min/max + gated honored; modifier deterministic; no file writes (verified by patching `builtins.open`) |
| `tests/test_statement_manager.py` | 9 | parser produces ParsedStatement; pre-computed analysis path; manager.process returns typed result; no real LLM call (`core.llm.chat`/`chat_strict` patched); tolerates None text |
| `tests/test_capability_manager.py` | 16 | enabled/limited/draft_only/nearby_alternative/disabled resolutions; config override; unknown stable; messenger_reply / create_document semantics; no external action (mocked `requests.post` records 0 calls); debug_state |
| `tests/test_director_manager.py` | 6 | DirectorDecision returned; no input mutation; social hints appended for jealousy_context; consumes CapabilityResolution; serializable |
| `tests/test_response_manager.py` | 6 | delegates to ResponseStage (verified via fake stage); preserves repair-reason chaining including `post_stale:`; no TTS/STT attribute; no behavior regression |
| `tests/test_extractor_model_profile.py` | 9 | response = MODEL; extractor uses EXTRACTOR_MODEL when set; falls back to MODEL when unset / disabled; default temperature ≤ 0.1; `chat_with_profile` delegates to chat() when model == MODEL; uses direct requests.post for other models with profile model in body; no real Ollama call in tests |
| `tests/test_boundary_rule_tables.py` | 7 | cold/reserved/cautious/comfort caps documented; cautious allows lists; cold blocks lists; unknown stage returns None; serializable |
| `tests/test_runtime_events.py` | 8 | sanitizer drops forbidden keys recursively + nested; buffer ring caps at 200; sink failures swallowed; sink receives event when set; truncation of long strings |

New docs: `docs/architecture/MANAGER_LAYER.md`,
`docs/architecture/STATE_OWNERSHIP.md`,
`docs/architecture/STATEMENT_MANAGER.md`,
`docs/architecture/CAPABILITY_MANAGER.md`,
`docs/architecture/RUNTIME_EVENTS.md`.

Validation:

```bash
python -m py_compile \
  core/engine/subsystems.py core/engine/brain.py \
  core/runtime/__init__.py core/runtime/modifications.py core/runtime/events.py \
  core/perception/__init__.py core/perception/statement.py \
  core/perception/parser.py core/perception/modifier.py core/perception/manager.py \
  core/capabilities/permissions.py core/capabilities/manager.py \
  core/capabilities/boundary_rules.py \
  core/engine/director_manager.py core/engine/response_manager.py \
  core/memory/manager.py core/llm.py config.py

pytest tests/test_brain_subsystem_base.py tests/test_statement_modifier.py \
       tests/test_statement_manager.py tests/test_capability_manager.py \
       tests/test_director_manager.py tests/test_response_manager.py \
       tests/test_extractor_model_profile.py tests/test_boundary_rule_tables.py \
       tests/test_runtime_events.py -q

pytest -q
```

Result: targeted `89 passed`; full suite **`979 passed`** (was 890
baseline, **+89 tests**).

What was intentionally NOT migrated:

- Existing pipeline stages remain unchanged. The active path
  (`MariaBrain.handle_message`) still drives the pipeline through stage
  instances. Managers are available for new callers; future stages can
  opt-in to routing through them.
- `MemoryManager.build_update_plan` returns `[]` — persistence writes
  stay in `PersistenceStage`.
- The `extract_all` LLM call still uses `chat_strict()` (response model)
  — switching it to `chat_with_profile("extractor", ...)` is the
  natural first follow-up calibration that doesn't touch behavior.
- `policy.py` / `handlers.py` / `reply_repair.py` have NOT been
  refactored to delegate availability to `CapabilityManager` — the
  manager runs in parallel today, future stages can fold them in.
- TTS/STT — explicitly out of scope; reserved for `0.20.4.x`.

Behavior preserved:

- no new mandatory LLM call;
- no behavior change in extractor / breakup / mood / capability matrix
  (all 0.20.1.x and 0.20.3.x scenario / invariant tests still pass);
- legacy `brain.memory` / `brain.director` / `brain.response` STAY
  pipeline stages (`tests/test_brain_facade.py` unchanged);
- `chat()` / `chat_strict()` signatures unchanged;
- `data/` schema additive — no migration required.

0.20.3.x branch closure (final):
```
0.20.3.1  ✅ Humor schema
0.20.3.2  ✅ Humor effects & rudeness correction
0.20.3.3  ✅ People mentions & social store
0.20.3.4  ✅ Relationship graph & scene roles
0.20.3.5  ✅ Attachment vulnerability state
0.20.3.6  ✅ Emotional events / recovery / relapse
0.20.3.7  ✅ Director integration & prompt blocks
0.20.3.8  ✅ Debug API & manual test campaign
0.20.3.9  ✅ Adaptive shared humor
0.20.3.10 ✅ Runtime architecture hardening / scalability foundation
```

The Social Perception & Attachment Vulnerability branch is fully
implemented AND the project is now structurally prepared for the
`0.20.4.x` Voice / TTS / STT branch. Suggested first voice stage:
`0.20.4.0 — Voice runtime contracts` (typed `VoiceContext`,
`VoiceManager` facade, `tts_started` / `stt_low_confidence` /
`playback_interrupted` runtime events).

---

## 0.20.4.0a — Voice-ready runtime cleanup

Goal:
Подготовить runtime под Voice/TTS/STT через извлечение per-turn
execution тела из `MariaBrain` в отдельный `TurnExecutor`. Сам voice
в этом этапе НЕ реализуется.

Changed:
- Добавлен `core/engine/turn_executor.py` — `TurnExecutor` с методами
  `execute_locked(brain, ctx, source, user_input, request_id)` и
  `handle_breakup_ended(...)`. Stateless, композиция (НЕ subclass
  MariaBrain).
- `core/engine/brain.py`:
  - `MariaBrain.__init__` добавил `self.turn_executor = TurnExecutor()`.
  - `_handle_message_locked` и `_handle_breakup_ended` — тонкие
    compat wrappers, делегируют `turn_executor.*`. Сигнатуры
    сохранены на случай внешних патчей.
  - Импорт `TurnExecutor` добавлен; brain.py уменьшился
    приблизительно на ~130 LOC активного pipeline-кода.
- Добавлен `tests/test_turn_executor.py` (9 тестов):
  composition-контракт, делегация handle_message → execute_locked,
  TurnExecutor не subclass MariaBrain, сохранение legacy stage- и
  manager-attrs, smoke payload shape, 403-path при ended.
- Добавлен `docs/architecture/TURN_EXECUTOR.md`.

Намеренно НЕ тронуто:
- Lock-model (один глобальный turn-lock сохранён, split отложен).
- `chat_pipeline as cp` namespace seam сохранён.
- Stage pipeline и manager-layer без изменений.
- Public API response shape без изменений.
- Reply repair / stale guard ordering без изменений.
- Capability/social/emotional поведение без изменений.
- TTS/STT/VoiceManager — out of scope.

Validation:
```bash
python -m py_compile core/engine/turn_executor.py core/engine/brain.py
pytest tests/test_turn_executor.py tests/test_brain_facade.py \
       tests/test_pipeline_e2e.py tests/test_pipeline_runtime_history.py \
       tests/test_brain_subsystem_base.py tests/test_response_manager.py \
       tests/test_capability_manager.py -q
pytest -q
```

Result:
- targeted: 65 passed
- full suite: **988 passed** (was 979, +9 tests)
- Performance: no observable change (32s suite time).

Risks:
- `brain.runtime.lock` всё ещё блокирует весь turn — voice streaming
  потребует lock-split (отложено в 0.20.4.0d).
- Транзиционная сигнатура `execute_locked(brain, ...)` передаёт
  весь brain, а не узкие зависимости. Это сознательное упрощение,
  ужесточение контракта — отдельный патч (Dependencies dataclass).

Next recommended:
- `0.20.4.0b` — VoiceManager contracts (`VoiceContext`,
  `tts_started` / `stt_low_confidence` / `playback_interrupted`
  runtime events, пустой facade).
- `0.20.4.0c` — TTS text preprocessing integration tests против
  реальных реплик из `data/history/*.jsonl`.
- `0.20.4.0d` — lock-split design note.

---

## 0.20.4.0b — VoiceManager contracts

Goal:
Зафиксировать границу между текстовым chat-pipeline'ом и будущим
voice-runtime'ом. **Contracts-only**: реальный TTS/STT, микрофон,
playback в этом этапе НЕ активируются.

Changed:
- Добавлен `core/voice/manager.py` (~370 LOC):
  - типы `VoiceContext`, `VoiceSpeakResult`, `VoiceTranscribeResult`,
    `VoiceState`;
  - класс `VoiceManager(BrainSubsystem)` — composition-owned facade
    с методами `speak`, `transcribe`, `mark_playback_started`,
    `mark_playback_interrupted`, `current_state`, `debug_state`;
  - три runtime-event константы: `tts_started`,
    `playback_interrupted`, `stt_low_confidence` + emit-хелперы
    `emit_tts_started` / `emit_playback_interrupted` /
    `emit_stt_low_confidence`, все через
    `core.runtime.events.record_runtime_event` с уже существующей
    sanitization-логикой.
- `core/engine/brain.py`:
  - импорт `VoiceManager`;
  - `MariaBrain.__init__` добавил `self.voice = VoiceManager()`
    (без provider'ов — disabled-defaults);
  - `subsystems` map включает ключ `"voice"`.
- Добавлен `tests/test_voice_manager.py` (17 тестов): composition,
  не-subclass, subsystems map, disabled defaults, speak/transcribe
  поведение, runtime event emission, `playback_interrupted` только
  при активном playback, forbidden keys не утекают, текстовый
  pipeline остаётся независимым.
- Добавлен `docs/architecture/VOICE_MANAGER.md`.

Намеренно НЕ тронуто:
- Реальные TTS/STT провайдеры (`TTSService`, `Pyttsx3TTSProvider`,
  `SileroTTSProvider`, `VADVoiceListener`) существуют, но в
  VoiceManager не инжектируются — это `0.20.4.1+`.
- HTTP endpoint'ы для Unity (`/api/unity/voice/*`) — это `0.20.4.4`.
- TurnExecutor / chat_pipeline / stages — VoiceManager НЕ вызывается
  из turn'а ни в одной точке. Текстовый path independent.
- Lock-model — VoiceManager не держит `runtime.lock`.
- `chat_pipeline as cp` seam — не тронут.

Validation:
```bash
python -m py_compile core/voice/manager.py core/engine/brain.py
pytest tests/test_voice_manager.py tests/test_turn_executor.py \
       tests/test_brain_subsystem_base.py tests/test_brain_facade.py \
       tests/test_runtime_events.py -q
pytest -q
```

Result:
- targeted: 56 passed
- full suite: **1006 passed** (was 988, +18 tests)

Risks:
- VoiceManager dispatch — exception-tolerant (defensive try/except в
  speak/transcribe), но реальные провайдеры могут уйти в долгий
  блокирующий вызов. Подключать их нужно с явным timeout-контрактом
  в `0.20.4.1` / `0.20.4.2`.
- Runtime-event buffer = 200 событий. Большая voice-сессия
  (десятки tts_started/stt_low_confidence в минуту) вытеснит
  турнирные события из buffer'а. Возможно потребуется отдельный
  voice-event subscriber вместо общего ring buffer'а — design
  решение для `0.20.4.0d`.

Next recommended:
- `0.20.4.0c` — TTS text preprocessing integration tests: прогнать
  50 реальных реплик Маши из `data/history/*.jsonl` через
  `core.voice.tts_text.prepare_maria_tts_text` и убедиться что
  декораторы (`*действие*`, `**окружение**`), `~`, `((`, эмодзи и
  CJK punctuation не уходят в синтезатор.
- `0.20.4.0d` — lock-split design note.
- `0.20.4.1` — TTS provider pipeline: собрать TTSService через
  `build_tts_service_from_config()`, передать в
  `VoiceManager(tts_service=...)`.

---

## 0.20.4.0c — TTS text preprocessing integration tests

Goal:
До подключения реальных TTS-провайдеров (0.20.4.1+) убедиться, что
`prepare_maria_tts_text` корректно срезает всё, что синтезатор не
должен произносить — на реальных репликах Маши из `data/history/*.jsonl`
плюс синтетических edge-кейсах.

Changed:
- Добавлен `tests/test_0204_tts_text_history_integration.py` (22 теста):
  - history-driven: прогон до 200 `role=bot` реплик через
    `prepare_maria_tts_text`, asserts на отсутствие `*`, `**`, `` ` ``,
    `~`, эмодзи в результирующем тексте, ограничение длины,
    rate_multiplier в безопасных границах [0.70..1.40],
    chunk-длина ≤ 220;
  - синтетические edge-кейсы: `*действие*`, `**окружение**`, `~`,
    `((`, `))`, `` `code` ``, emoji 😊, `**bold** + *italic*`;
  - tail-cases: пустая строка, whitespace-only, action-only реплика,
    truncation до max_chars, ellipsis-normalization, rate-bounds для
    разных emotion-профилей.
- **Real bug fix found by tests**: `_clean_markers` не вырезал
  Unicode-эмодзи. Persona запрещает эмодзи в ответе Маши, но LLM
  иногда пропускает — синтезатор не должен «произносить» их как
  мусор. Добавлен `_EMOJI_RE` (диапазоны Emoji-Symbols / Faces /
  Supplemental / Dingbats / Flags) и шаг `_EMOJI_RE.sub("", text)`
  в `_clean_markers`. Существующие 6 unit-тестов
  `test_0202_maria_tts_text.py` не сломались.

Намеренно НЕ тронуто:
- `prepare_maria_tts_text` API — без изменений.
- `TTSService`, `Pyttsx3TTSProvider`, `SileroTTSProvider` — не
  подключены.
- `VoiceManager` — не вызывался в этом этапе.

Validation:
```bash
pytest tests/test_0204_tts_text_history_integration.py \
       tests/test_0202_maria_tts_text.py -q
pytest -q
```

Result:
- targeted: 28 passed (22 новых + 6 существующих)
- full suite: **1028 passed** (was 1006, +22)

Risks:
- Diapason эмодзи в `_EMOJI_RE` не покрывает 100% Unicode-эмодзи
  (нет skin-tone modifiers `U+1F3FB-U+1F3FF`, zero-width joiner
  последовательности). Для большинства реплик достаточно — но
  при появлении в истории редких эмодзи может потребоваться
  расширение.
- History-driven тесты пропускаются (`pytest.skip`) если
  `data/history/` пуст. Защищено `<5%` ratio empty-after-prep.

Next recommended:
- `0.20.4.0d` — lock-split design note (без кода).
- `0.20.4.1` — TTS provider pipeline в VoiceManager.

---

## 0.20.4.0d — Lock model design note

Goal:
**Design-only этап.** Зафиксировать текущую lock-модель,
требования voice-runtime'а и план split'а на `pipeline_lock` /
`state_lock` / `voice_lock` БЕЗ реализации. Реальный split —
в `0.20.4.3`.

Changed:
- Добавлен `docs/architecture/LOCK_MODEL_DESIGN.md`:
  - что текущий `runtime.lock` защищает (история, counter,
    last_analysis, store, cross-stage инварианты);
  - что он защищать НЕ должен (LLM, embed, TTS, STT);
  - voice-сценарии (одновременный TTS+chat, STT-streaming,
    barge-in, proactive, /api/state без блокировки);
  - целевая модель `pipeline_lock` (per session) / `state_lock`
    (короткий, вокруг persistence) / `voice_lock` (playback FSM);
  - явные anti-deadlock правила: порядок acquisition;
  - barge-in flow;
  - почему 0.20.4.1 и 0.20.4.2 можно делать на текущей модели;
  - что НЕ делать в split'е (asyncio, multiprocessing,
    distributed lock'и — out of scope для local single-user).

Намеренно НЕ тронуто:
- Никакого кодового diff'а. `runtime.lock` остался один.
- Никаких новых тестов — этот этап не вводит контракты, фиксирует
  только архитектурный план.

Validation:
```bash
pytest -q
```

Result:
- full suite: **1028 passed** (без изменений с 0.20.4.0c).

Risks:
- Документ может разойтись с реальностью к моменту 0.20.4.3 если
  voice-runtime потребует другой формы lock'а (например,
  `asyncio.Lock`). В этом случае документ должен быть обновлён
  ДО кода.

Next recommended:
- `0.20.4.1` — TTS provider pipeline: собрать `TTSService` через
  `build_tts_service_from_config()`, передать в
  `VoiceManager(tts_service=...)`. Текущая lock-модель достаточна.
- `0.20.4.2` — STT provider pipeline.
- `0.20.4.3` — Playback / interruption controller + lock-split
  (`pipeline_lock` / `state_lock` / `voice_lock`).

---

## 0.20.4.0e — RU documentation refresh / project state cleanup

Goal:
Освежить human-facing документацию после закрытия `0.20.3.x` и
завершения `0.20.4.0a–d` voice-ready cleanup. Поведение кода НЕ
меняется. Технические идентификаторы (классы, capability names,
relationship stages, runtime event names, prompt block markers) —
остаются в английском.

Changed:
- `Project_state.md` — полностью переписан. Старый baseline-список
  («persona + CORE_PERSONA, affection / trust, …») заменён на
  current checkpoint, completed roadmap, architecture summary,
  voice readiness таблицу, next stage, known risks и stable areas.
- `README.md` (backend) — обновлена структура (apps/, capabilities/,
  social/, perception/, runtime/, voice/), добавлены секции
  Architecture overview / Roadmap / Manual validation / ссылки на
  `docs/architecture/`.
- `RUN_CAPABILITY_SCENARIOS.md` — добавлен status-баннер «capability
  branch closed → smoke checks»; PowerShell-safe relmatrix loop
  (`New-Item -ItemType Directory -Force ... | ForEach-Object`);
  упоминание стейл-файла `horizon_relmatrix_04_comfort_mood_sensitive.json`.
- `README.md` (root, `D:/Projects/Maria/README.md`) — заголовок и
  pointer-блок на backend docs, `UNITY_HANDOVER.md`;
  `ollama pull mistral` → `gemma3:4b` с пояснением.
- `DEV_LOG.md` — эта запись.

Канонический README — `backend/README.md` (полный). Корневой
`README.md` оставлен коротким pointer'ом (никакого `ReadME.md` в дереве
нет, дубль не нужен).

Намеренно НЕ тронуто:
- `docs/architecture/*.md` — большинство файлов уже на русском и
  привязаны к конкретным stage-tag'ам (`MANAGER_LAYER`,
  `STATEMENT_MANAGER`, `CAPABILITY_MANAGER`, `RUNTIME_EVENTS`,
  `TURN_EXECUTOR`, `VOICE_MANAGER`, `LOCK_MODEL_DESIGN`,
  `STATE_OWNERSHIP`). Они актуальны после 0.20.4.0d.
- `*_DRAFT.md` в `docs/architecture/` — это технические specs
  (English-by-design); их переписывать в RU не безопасно без
  верификации стадии.
- Тесты, сценарные JSON, API-контракты, capability names,
  relationship stages, runtime event names, prompt block markers.
- Старый legacy-блок DEV_LOG (`0.1.x – 0.17.1`) — не переписывается.

Validation:
```bash
python -m compileall -q .
pytest tests/test_brain_facade.py tests/test_turn_executor.py \
       tests/test_capability_manager.py tests/test_runtime_events.py -q
pytest -q
```

Result:
- compileall: OK
- targeted: 39 passed
- full suite: **1028 passed** (без изменений с 0.20.4.0d).

Next recommended:
- `0.20.4.1` — TTS provider pipeline.

---

## 0.20.4.0f — Delayed humor resolution + thought cleanup

Goal:
Две связанные проблемы, замеченные в ручном тестировании:
1. Юмор слишком часто классифицируется в один turn и сразу едет в drift,
   даже когда смысл шутки не понят.
2. Legacy `thought` строки («Мне хочется отстраниться…») превратились в
   шум: они дублируют то, что уже выражают director / capability /
   social audits / emotional health, и попадают в payload.

Changed:

- **Humor lifecycle (новое):**
  - `core/brain/humor/pending.py` — `PendingHumorEvent`,
    `HumorResolution`, `PendingHumorBuffer` (runtime-only ring buffer
    cap=20). Никаких persistent JSON-файлов.
  - `core/brain/humor/schema.py` — `HumorAnalysis` теперь имеет
    `status` (`none / possible / confirmed / rejected / expired`)
    и `needs_followup`. Backward-compatible: legacy callers без
    `status` интерпретируются как `confirmed`. `HUMOR_STATUSES`
    добавлены в публичный API.
  - `core/brain/humor/detector.py`:
    - markers разделены: `_LAUGHTER_MARKERS` («хах», «ахах», «)))»)
      vs `_SOFT_SMILE_MARKERS` («)», «~», «:)») — одиночный `)`
      больше не классифицирует как юмор;
    - `_looks_like_soft_smile()` — отдельный helper;
    - absurd-эвристика one-liner ("Осьминог — подумал Штирлиц") →
      humor_type=`absurd`, status=`possible`;
    - в конце `_infer_humor` присваивается `status`: offensive/
      boundary_testing всегда `confirmed`; absurd one-liner и
      low-confidence без laughter → `possible`.
  - `core/brain/humor/drift.py` — `compute_humor_drift` early-returns
    пустым dict при `status in ("possible", "rejected", "expired")`.
    Legacy callers без status работают как раньше.
  - `core/brain/humor/shared_humor.py` — `_humor_to_compact_event`
    отклоняет `possible`/`rejected`/`expired`. Confirmed события
    продолжают писаться в `data/humor_events.jsonl`.
- **Path wiring:**
  - `core/engine/runtime_context.py` — `RuntimeContext` владеет
    `pending_humor: PendingHumorBuffer` (runtime-only).
  - `core/engine/active_path.py` — `_maybe_record_pending_humor()`
    создаёт `PendingHumorEvent` после `handle_message`, если
    `analysis["humor"]["status"] == "possible"`. Эмитит
    `pending_humor_created` runtime event. `last_meta` теперь
    содержит `pending_humor_delta`.
  - `core/engine/offline_cognition_path.py` —
    `resolve_pending_humor()` детерминированно классифицирует
    PendingHumorEvent'ы по subsequent user-сообщениям: CONFIRM_MARKERS
    («хах», «это шутка»), FAILED_CONFIRM_MARKERS («ты не поняла»),
    REJECT_MARKERS («я не шучу», «это серьёзно»). Эмитит
    `pending_humor_confirmed/rejected/expired` runtime events. Не
    делает LLM-вызовов.
- **Thought cleanup:**
  - `config.py` — `DEBUG_EXPOSE_THOUGHTS = False` (env override через
    `MARIA_DEBUG_EXPOSE_THOUGHTS`).
  - `core/engine/message_context.py` — добавлены
    `internal_orientation: str = "neutral"` и
    `reason_codes: list[str]`.
  - `core/engine/turn_executor.py` — payload теперь содержит
    `internal_orientation`, `reason_codes`; `thought` показывается
    только при `DEBUG_EXPOSE_THOUGHTS=True`, иначе `""`.
  - `core/engine/brain.py` regen-payload — то же gate.
  - RuntimeEvent sanitizer (`core/runtime/events.py`) уже стрипал
    `thought`/`raw_thought`/`system_prompt` — без изменений.
  - TTS preprocessing (`core/voice/tts_text.py`) уже не пускает
    `*action*` / `~` / эмодзи — без изменений.
- **Tests:**
  - Новые: `tests/test_humor_pending_resolution.py` (15),
    `tests/test_thought_visibility.py` (6).
  - Обновлены: `tests/test_brain_facade.py`, `tests/test_pipeline_e2e.py`
    — теперь ожидают `thought == ""` и проверяют наличие
    `internal_orientation`.

Намеренно НЕ тронуто:
- Реальные TTS/STT провайдеры.
- Lock model — не изменён.
- Extractor LLM prompts — `core/prompts/technical.py` не переписан
  (это отдельная задача, чтобы не ломать существующие scenario
  тесты на конкретные ответы модели).
- `chat_pipeline as cp` seam — не тронут.
- Persistent `data/humor_pending_events.json` — НЕ создан;
  pending events живут только в runtime.

Validation:
```bash
python -m compileall -q .
pytest tests/test_humor_pending_resolution.py \
       tests/test_thought_visibility.py \
       tests/test_humor_schema.py tests/test_humor_effects.py \
       tests/test_adaptive_humor_taste.py \
       tests/test_brain_facade.py tests/test_turn_executor.py \
       tests/test_pipeline_e2e.py tests/test_runtime_events.py -q
pytest -q
```

Result:
- targeted: 21 новых + все humor / brain / pipeline существующие зелёные.
- full suite: **1049 passed** (was 1028, +21).

Risks:
- `_looks_like_absurd_oneliner` — конструктивно консервативная
  эвристика; ложноположительные ловит только короткие
  «X — подумал Y» формы. Если в дальнейшем будут реальные
  short messages с тире и глаголом, может пометить как absurd
  ошибочно. Митигация: status=`possible` (не drift), expires за 3
  turn'а без подтверждения.
- Runtime-only буфер: при рестарте процесса unresolved события
  теряются. Сознательное упрощение — pending events недолгоживущие
  по дизайну.
- Extractor prompts всё ещё не описывают `status`/`needs_followup`.
  Сейчас detector сам нормализует и проставляет — когда extractor
  начнёт возвращать `humor.status` напрямую, нормализатор это
  подхватит, но без обновления prompt LLM будет редко выставлять
  `possible` руками. Это OK — fallback-эвристика покрывает основные
  случаи.

Next recommended:
- `0.20.4.0g` — Dialogue continuity / stale regen calibration,
  ИЛИ
- `0.20.4.1` — TTS provider pipeline (как было запланировано).

---

## 0.20.4.0g — Dialogue continuity / stale regen calibration

Goal:
Закрыть две проблемы из ручных логов после `0.20.4.0f`:
1. `regenerate_last_reply()` иногда заменял релевантный ответ на stale-реплику
   из старой темы, особенно после коротких follow-up вопросов (`Веришь?`,
   `А если попробовать угадать?)`).
2. Legacy `thought` уже был скрыт в normal payload, но мог продолжать попадать
   в `data/history/*.jsonl` через `chat_log` / `PersistenceStage` / regen meta.

Changed:

- **Dialogue continuity guard:**
  - Новый `core/engine/dialogue_continuity.py`:
    - `content_tokens()` / `normalize_text()` — лёгкая нормализация topic anchors;
    - `is_short_followup()` — короткие follow-up turns, где regen чаще всего
      съезжает в старую тему;
    - `looks_like_topic_jump()` — проверяет, содержит ли regen candidate
      topic-токены из более старой истории, отсутствующие в текущем user turn +
      старом ответе;
    - `choose_regen_reply()` — если candidate прыгает к старой теме, а старый
      ответ был чище, оставляет old reply вместо замены на худший regen.
  - `core/engine/brain.py`:
    - `regenerate_last_reply()` теперь прогоняет финальный candidate через
      `choose_regen_reply()`;
    - в payload и chat_log meta добавляется `regen_continuity_guard` с reason /
      stale_tokens для debug;
    - если candidate rejected, action/actions сбрасываются, чтобы не сохранять
      действия от отвергнутой stale-реплики.

- **History thought privacy:**
  - `core/memory/chat_log.py`:
    - добавлен `_sanitize_meta()`;
    - по умолчанию (`DEBUG_EXPOSE_THOUGHTS=False`) `thought` / `raw_thought`
      не пишутся в history JSONL;
    - `None`-значения удаляются из meta;
    - `replace_last_bot()` применяет тот же sanitizer после merge meta.
  - `core/engine/stages/persistence_stage.py`:
    - оставлен compatibility path: stage всё ещё передаёт `thought` в meta,
      но финальный guard теперь централизован в `chat_log`.

- **Tests:**
  - Новый `tests/test_dialogue_continuity_regen.py` (5):
    - short follow-up detection;
    - stale topic anchor detection на ручном кейсе `посплетничаем`;
    - `choose_regen_reply()` оставляет старый релевантный ответ;
    - валидный candidate типа `Ну, логично ведь, да?` принимается;
    - full regen path не заменяет ответ stale topic candidate.
  - Обновлён `tests/test_memory_persistence_fixes.py`:
    - `chat_log.replace_last_bot()` больше не сохраняет `thought` по умолчанию;
    - debug flag `DEBUG_EXPOSE_THOUGHTS=True` сохраняет thought/raw_thought для
      explicit debug режима.

Намеренно НЕ тронуто:
- Реальные TTS/STT провайдеры.
- Lock model.
- `chat_pipeline as cp` seam.
- Extractor LLM prompts.
- Relationship/capability/social/emotional coefficients.
- Stale guard в обычном `ResponseStage` — этот patch калибрует именно regen,
  где manual logs показали явную замену хорошего ответа на stale-кандидат.

Validation:
```bash
python -m compileall -q core tests
pytest tests/test_dialogue_continuity_regen.py \
       tests/test_memory_persistence_fixes.py \
       tests/test_thought_visibility.py \
       tests/test_brain_facade.py \
       tests/test_turn_executor.py -q
pytest -q
```

Result:
- targeted: **41 passed**.
- full suite in this environment: collection stops on missing optional Flask
  dependency (`ModuleNotFoundError: No module named 'flask'` in
  `tests/test_social_debug_api.py` / `tests/test_webui_jobs_api.py`). This is
  environment-limited; `requirements.txt` contains Flask and the patch itself
  does not touch WebUI imports.

Risks:
- Topic-jump detector is intentionally lexical and conservative. It can miss
  semantic stale replies that do not reuse older topic anchors.
- It is safer to reject only obvious old-anchor jumps than to over-repair
  natural Maria replies.
- If future regen gets more semantic context, this helper should stay as a
  safety net, not become a full dialogue-understanding layer.

Next recommended:
- `0.20.4.1` — TTS provider pipeline, or
- small manual-log calibration pass if another stale/regen family appears.

---

## 0.20.4.1 — Voice Reaction Tags & SpeechOrchestrator Foundation

Goal:
Заменить идею «free-text `thought` управляет голосом» на структурированный
voice-planning слой: публичные сигналы turn'а (`internal_orientation`,
`reason_codes`, `analysis.emotion`, `humor.status`, irritation,
capability boundary, emotional health) переводятся в `VoiceReactionTags`,
а затем в детерминированный `SpeechPlan`. Реальный TTS НЕ активируется,
auto-speak не включается, payload не меняется.

Boundary spec (`docs/old_roadmaps/voice_profile_boundary.md`)
зафиксировал: `core/voice/*` остаётся **generic engine layer**. Никакого
hardcoded Maria-поведения внутри core/voice, никакого импорта из
`core.personas.*`. Персональная стилистика инжектируется через
`VoiceStyleProfile`.

Added:

- **`core/voice/speech_plan.py`** (новый):
  - `VoiceStyleProfile` (frozen dataclass): `default_provider_hint`,
    `default_voice`, `base_speed`, `base_volume`,
    `orientation_delivery_map`, `emotion_delivery_map`,
    `speed_bias_by_tag`, `volume_bias_by_tag`, `pause_bias_by_tag`,
    `max_interruption_sensitivity`. Все mapping-поля по умолчанию
    пустые — generic движок не знает ни про shy, ни про playful.
  - `SpeechPlan`, `SpeechPlanSegment`, `PauseDirective` — frozen
    dataclasses, никаких полей `thought`/`raw_thought`.
  - Clamp-границы: `SPEED_MIN=0.75`, `SPEED_MAX=1.25`, `VOLUME_MIN=0.60`,
    `VOLUME_MAX=1.15`, `PAUSE_MS_MAX=1200`, `MAX_SEGMENTS=24`.
  - Helpers: `clamp_speed`, `clamp_volume`, `clamp_pause_ms`,
    `clamp_unit` (NaN / non-numeric safe).
  - Singletons: `NEUTRAL_VOICE_PROFILE`, `EMPTY_SPEECH_PLAN`.

- **`core/voice/reaction.py`** (новый):
  - `VoiceReactionTags`: `orientation`, `emotion_tags`, `delivery_tags`,
    `reason_codes`, `interruption_sensitivity`, `can_auto_speak`.
  - `KNOWN_ORIENTATIONS` = `{neutral, shy, guarded, playful, tired, warm,
    sad, embarrassed, curious, annoyed}` — generic набор. Неизвестный
    orientation НЕ роняет builder: нормализуется в `"neutral"`, в
    `reason_codes` дописывается `"unknown_orientation"`.
  - `build_voice_reaction_tags(*, reply, analysis, internal_orientation,
    reason_codes, humor_status, aff_state, capability_resolution,
    emotional_health, dialogue_ended, voice_enabled, profile)`:
    - **сигнатура не принимает `thought` / `raw_thought`** (проверяется
      тестом через `inspect.signature`);
    - `emotion_tags` берётся только из публичного `analysis["emotion"]`
      + `intensity` (`>=0.7 → "intense"`) + `humor_status=="confirmed"
      → "humor_confirmed"`;
    - `delivery_tags` — пусто, если profile не передан; с profile
      собираются из `orientation_delivery_map[orientation]` и
      `emotion_delivery_map[emotion]`, deduped с сохранением порядка;
    - `interruption_sensitivity` clamped в `[0, profile.max_…]`;
      повышается при irritation≥50, shy/embarrassed/sad/guarded,
      emotional_health.strain≥0.6, capability `boundary_mode in
      {soft, hard}`;
    - `can_auto_speak=False` при empty reply / `dialogue_ended=True`
      / `voice_enabled=False`, с соответствующими reason_codes.

- **`core/voice/orchestrator.py`** (новый):
  - `SpeechOrchestrator(profile: VoiceStyleProfile | None = None)`.
    Без профиля используются нейтральные дефолты.
  - `build_plan(reply, *, tags, emotion=None, request_id=None) →
    SpeechPlan`:
    - использует существующий `prepare_maria_tts_text` —
      `spoken_text` гарантированно без `*action*`, `**env**`, эмодзи;
    - применяет `profile.speed_bias_by_tag` / `volume_bias_by_tag`
      мультипликативно к `delivery_tags`, затем clamp;
    - `pause_bias_by_tag` добавляется к `pause_after_ms` промежуточных
      сегментов (последний сегмент без хвостовой паузы), clamp по
      `PAUSE_MS_MAX`;
    - `PauseTag` из `PreparedTTS` → `PauseDirective` 1:1, с clamp;
    - `len(segments) <= MAX_SEGMENTS`;
    - при `tags.can_auto_speak=False` или пустом reply — возвращает
      пустой план с сохранением profile-дефолтов provider/voice и
      `source_reply_id`.
  - **НЕ** импортирует `core.personas.*`. Persona-tuning приходит
    только через конструктор.

- **`core/voice/__init__.py`** — реэкспорт новых публичных типов
  (`VoiceReactionTags`, `VoiceStyleProfile`, `SpeechPlan`,
  `SpeechPlanSegment`, `PauseDirective`, `SpeechOrchestrator`, clamp
  helpers, neutral singletons).

Tests (35 новых):

- `tests/test_voice_reaction_tags.py` (14): builder signature без
  thought-полей; neutral inputs → neutral tags; unknown orientation →
  fallback + reason code; generic engine без profile не выдумывает
  delivery_tags; profile-driven delivery_tags с дедупом; humor_confirmed;
  irritation + boundary → sensitivity; dialogue_ended / empty reply /
  voice_disabled блокируют `can_auto_speak`; `asdict()` не содержит
  `thought`/`raw_thought`; clamp по `profile.max_interruption_sensitivity`;
  детерминизм при одинаковых входах.
- `tests/test_voice_style_profile.py` (8): дефолтный профиль neutral
  и пустой; `NEUTRAL_VOICE_PROFILE == VoiceStyleProfile()`; ни один из
  публичных атрибутов `core/voice/{reaction, speech_plan,
  orchestrator}.py` не приходит из `core.personas.*` (контрактный
  guard); `core.voice` импортируется без `core.personas.maria_voice`;
  clamp-границы; profile frozen.
- `tests/test_speech_orchestrator.py` (13): работает без profile;
  fake profile меняет `provider_hint`/`voice`; `*action*`/`**env**`/эмодзи
  не попадают в `spoken_text`; `segments <= MAX_SEGMENTS`;
  speed/volume clamp при экстремальных bias'ах; shy + ellipsis →
  speed<1 + `PauseDirective(kind="ellipsis")`; dialogue_ended / empty
  reply → empty plan; детерминизм; `request_id → source_reply_id`;
  `asdict(plan)` не содержит `thought`/`raw_thought`; `style_tags ==
  reaction.delivery_tags`; `pause_bias_by_tag` применяется с clamp.

Намеренно НЕ тронуто:
- `TurnExecutor` / payload — orchestrator пока ниоткуда не вызывается.
- `chat_pipeline as cp` seam.
- Lock model (`LOCK_MODEL_DESIGN.md`).
- Реальные TTS/STT провайдеры, auto-speak, аудио-устройства.
- Persona files (`core/personas/maria.py`, `data/persona.md`).
- `core/personas/maria_voice.py` / `.example` — boundary spec §6:
  «only when runtime/bootstrap needs it now». Foundation-only stage.
- LLM-промпты, extractor.
- Runtime events: новых типов не добавлено.
- Memory / persistent state.
- API endpoints: воксе-эндпойнтов на этом stage нет (см. SESSIONS note
  ниже).

Validation:
```bash
MariEnv\Scripts\python.exe -m compileall -q .
MariEnv\Scripts\pytest.exe tests/test_voice_reaction_tags.py \
       tests/test_voice_style_profile.py \
       tests/test_speech_orchestrator.py -q
MariEnv\Scripts\pytest.exe -q
```

Result:
- compileall: OK
- targeted (new tests): **35 passed**
- full suite: **1100 passed** (was 1065, +35).

Risks / limits:
- `KNOWN_ORIENTATIONS` — фиксированный generic set. Если бэкенд начнёт
  эмитить новые orientation literal'ы, они нормализуются в neutral до
  явного добавления — это сознательный safety net, а не баг.
- `_apply_speed_biases` использует базу `prepared.rate_multiplier`
  (из `prepare_maria_tts_text`), затем `profile.base_speed`, затем
  per-tag bias. Все произведения клампятся; экстремальные bias'ы (как
  `0.01` или `99.0`) не выпадают за `[SPEED_MIN, SPEED_MAX]`.
- `pause_bias_by_tag` накапливается только для промежуточных
  сегментов; финальный сегмент остаётся без хвостовой паузы —
  иначе bias ощущался бы как «вечный хвост» в конце реплики.
- `PauseDirective.after_index` — индекс в `spoken_text`, не в
  `segments`. Будущий TTS provider маппит pause-метки на SSML или
  audio post-processing, поэтому индекс относительно visible-text
  лучше, чем относительно сегмента.
- На этом stage no-op для существующего chat flow — `SpeechPlan` пока
  никем не потребляется. Интеграция через `VoiceManager` /
  `TurnExecutor` будет в более поздних stage'ах согласно roadmap.

Next recommended:
- `0.20.4.2` — Silero TTS Provider Pipeline + Cache Cleanup
  (включает интеграцию `SpeechOrchestrator` в `VoiceManager`,
  cache dir, optional auto-speak, новые runtime events).

---

## 0.20.4.2 — Silero TTS Provider Pipeline + Cache Cleanup

Goal:
Довести существующий TTS-stack (`TTSService` / `SileroTTSProvider` /
`Pyttsx3TTSProvider` / `SilentDebugTTSProvider`) до runtime-ready
состояния: расширить voice runtime events, добавить cache cleanup
policy, optional auto-speak gate. Реальный текстовый pipeline остаётся
неизменным — auto-speak отключён по дефолту, `TurnExecutor` не тронут.

Added:

- **`core/voice/cache.py`** (новый):
  - `CacheCleanupResult` (frozen): `cache_dir`, `removed`, `kept`,
    `bytes_removed`, `skipped`.
  - `cleanup_voice_cache(cache_dir, *, max_files, max_mb, max_age_hours,
    emit_event=True, request_id, conversation_id)`:
    - Маска `*.wav` (не трогает другие файлы — `.txt`, индексы, мусор
      воркфлоу).
    - Eviction order: age → total size (MB) → file count.
      Удаление oldest-first внутри каждого прохода.
    - Лимиты `<=0` → axis отключён; `Path` не существует → `skipped=True`.
    - Эмитит `tts_cache_cleaned` только если действительно удаляли
      файлы. Provider-агностичен (не зависит от Silero/torch).

- **`core/voice/auto_speak.py`** (новый):
  - `should_auto_speak(*, reply, voice_enabled, auto_speak_enabled,
    dialogue_ended, reaction)` → `(allow, reason)`. Стабильные reason
    коды: `voice_disabled`, `auto_speak_disabled`, `empty_reply`,
    `dialogue_ended`, `reaction_blocked`, `ok`.
  - `maybe_auto_speak(reply, *, voice_manager, orchestrator,
    reaction, emotion, request_id, conversation_id, dialogue_ended,
    voice_enabled, auto_speak_enabled)`:
    - При отказе gate'а — НЕ вызывает провайдера, никаких runtime
      events, возвращает `VoiceSpeakResult(ok=False, reason=...)`.
    - При allow — строит `SpeechPlan` через `SpeechOrchestrator`
      (без profile = neutral по boundary spec), затем
      `voice_manager.speak(VoiceContext(text=plan.spoken_text, ...))`.
    - Если config-флаги не переданы — лениво читает `config`
      (`VOICE_TTS_ENABLED`, `VOICE_AUTO_SPEAK_ENABLED`).
  - **НЕ интегрирован в TurnExecutor / ResponseStage** — gate-helper
    для будущих интеграций (debug API, Unity bridge, manual scripts).

Changed:

- **`core/voice/manager.py`**:
  - 5 новых event-констант: `EVENT_TTS_FINISHED`, `EVENT_TTS_FAILED`,
    `EVENT_TTS_CACHE_HIT`, `EVENT_TTS_CACHE_CLEANED` (+ оригинальный
    `EVENT_TTS_STARTED`). `VOICE_EVENT_TYPES` расширен до 7 элементов
    (3 старых + 4 новых).
  - Helper'ы emit: `emit_tts_finished`, `emit_tts_failed`,
    `emit_tts_cache_hit`, `emit_tts_cache_cleaned`. Payload содержит
    только safe metadata (provider, audio_path, cached, text_length,
    duration_estimate_sec, reason / removed / kept / bytes_removed).
  - `_estimate_duration_sec(text_length)` — детерминированная
    эвристика без загрузки аудио (~14 chars/sec, clamped в [0.1, 120]).
  - `VoiceManager.speak()` теперь после `synthesize()` эмитит:
    - `tts_cache_hit` если `result.cached=True`;
    - `tts_finished` на успешном синтезе;
    - `tts_failed` на `ok=False` (с reason из `result.error`).
    Defensive `except` тоже эмитит `tts_failed`.

- **`core/voice/tts_service.py`**:
  - `VOICE_CACHE_DIR` default → `Path("data/voice/cache")` (старый
    `data/voice_cache` больше не дефолт). `data/**` уже в `.gitignore`
    — приватность сохранена.
  - `build_tts_service_from_config()` после построения провайдера
    запускает best-effort `cleanup_voice_cache()` с config knobs
    (`VOICE_TTS_CACHE_MAX_FILES`, `VOICE_TTS_CACHE_MAX_MB`,
    `VOICE_TTS_CACHE_MAX_AGE_HOURS`). Cleanup не блокирует bring-up:
    любая ошибка глотается и не мешает text-flow.

- **`config.py`** (targeted, без флипа существующих локальных значений):
  - `VOICE_TTS_CACHE_DIR = "data/voice/cache"` (был `"data/voice_cache"`).
  - `VOICE_AUTO_SPEAK_ENABLED = False` (новый, выключен по дефолту).
  - `VOICE_TTS_CACHE_MAX_FILES = 200`,
    `VOICE_TTS_CACHE_MAX_MB = 256`,
    `VOICE_TTS_CACHE_MAX_AGE_HOURS = 72`.
  - `VOICE_TTS_ENABLED` НЕ менялся (per spec: «do not silently change
    private local behavior»).

- **`core/voice/__init__.py`** — реэкспорт новых публичных типов
  (`CacheCleanupResult`, `cleanup_voice_cache`, `should_auto_speak`,
  `maybe_auto_speak`, новые event-константы).

Tests (27 новых):

- `tests/test_tts_provider_pipeline.py` (10): disabled VoiceManager
  не эмитит TTS events; silent_debug пишет wav без опциональных
  deps; `build_tts_service("silero")` не подгружает `torch`;
  speak-success эмитит started+finished с правильным payload'ом;
  cached-результат эмитит `tts_cache_hit` ДО `tts_finished`;
  failure эмитит `tts_failed` с reason; event payloads без
  forbidden keys; `build_tts_service_from_config()` уважает
  `VOICE_TTS_ENABLED=False` (disabled provider) и дефолтит cache
  под `data/voice/cache`.
- `tests/test_voice_cache_cleanup.py` (7): missing dir → `skipped`;
  age-eviction удаляет старые; max_files — oldest first; max_mb —
  oldest first до достижения предела; event эмитится только при
  реальных удалениях; payload sanitized (без `thought`/`raw_thought`/
  `system_prompt`/`api_key`/`token`); не-`.wav` файлы не трогаются.
- `tests/test_voice_auto_speak.py` (10): `should_auto_speak` блокирует
  при voice_disabled / auto_disabled / empty / dialogue_ended /
  reaction.can_auto_speak=False; allow при всех включённых;
  `maybe_auto_speak` отвергнутый gate'ом НЕ вызывает провайдера
  (last_request=None) и не эмитит TTS events; при включённом —
  возвращает audio metadata и шлёт через TTSRequest текст БЕЗ
  `*action*`/эмодзи (orchestrator чистит); pytest не подгружает
  `torch`; helper читает `config.VOICE_*` по умолчанию; emitted
  events без forbidden keys.

Также обновлён `tests/test_voice_manager.py::test_voice_event_types_*`
— ожидаемый set расширен до 7 типов.

Намеренно НЕ тронуто:
- `TurnExecutor` / `ResponseStage` / payload `/api/chat` — auto-speak
  пока никем не вызывается из turn'а. Это lock-split territory
  (`LOCK_MODEL_DESIGN.md` / `0.20.4.3`).
- `chat_pipeline as cp` seam.
- `core/personas/maria.py` / `data/persona.md`.
- `VOICE_TTS_ENABLED` — оставлен как есть в локальном config'е.
- Реальные Silero/torch вызовы в тестах — provider строится lazy,
  тесты используют FakeTTSService / SilentDebugTTSProvider.
- STT pipeline / microphone / playback controller — отдельные stage'ы.
- HTTP endpoint'ы — appear в 0.20.4.4 (Unity bridge) / 0.20.4.5
  (debug endpoints).
- Memory / persistent state / scenario-replay JSON.

Validation:
```bash
MariEnv\Scripts\python.exe -m compileall -q .
MariEnv\Scripts\pytest.exe tests/test_tts_provider_pipeline.py \
       tests/test_voice_cache_cleanup.py \
       tests/test_voice_auto_speak.py -q
MariEnv\Scripts\pytest.exe -q
```

Result:
- compileall: OK
- targeted (new tests): **27 passed**
- full suite: **1127 passed** (was 1100, +27).

Risks / limits:
- Default cache dir изменён с `data/voice_cache` на `data/voice/cache`.
  Старые wav-файлы в `data/voice_cache` станут осиротевшими (не
  обслуживаются cleanup'ом, кэш-хитов из них тоже не будет). На
  диске порядка десятков MB max, удаляются вручную. Не PII.
- `cleanup_voice_cache` синхронный. При очень большом кэше (тысячи
  файлов) первый запуск `build_tts_service_from_config()` может
  ощутимо подвиснуть. На стандартных лимитах (200 файлов / 256 MB /
  72 часа) этот эффект невидим.
- `_estimate_duration_sec` — heuristic 14 chars/sec. Реальный аудио
  файл может быть длиннее/короче; для UI/debug metadata этого
  достаточно, для precise scheduling — нет.
- `maybe_auto_speak` не интегрирован в pipeline. Это сознательное
  решение — не открываем lock-split раньше времени. Включение в
  TurnExecutor запланировано после lock split (`0.20.4.3`).
- Event sanitizer (`core.runtime.events._sanitize`) фильтрует
  forbidden keys на выходе. Все 4 новых TTS event'а тестируются
  на «нет thought/raw_thought/system_prompt/api_key/token».

Next recommended:
- `0.20.4.3` — Playback / Interruption Controller (включает lock
  split per `LOCK_MODEL_DESIGN.md`, реальный audio device wrapper,
  barge-in, и тут уже корректно интегрировать `maybe_auto_speak`
  в TurnExecutor).

---

## 0.20.4.3 — Playback / Interruption Controller

Goal:
Добавить logical playback state + interruption model. PlaybackController
описывает «играет / прервано / завершилось», превращает пользовательский
stop в social-visible runtime event, и считает recent interruption
counter для будущего director / offline-cognition consumer. Реальное
audio-устройство, lock split и TurnExecutor integration на этом stage'е
НЕ трогаем — controller это чисто logical state holder.

Added:

- **`core/voice/interruption.py`** (новый):
  - `INTERRUPTION_REASONS = {"user_stop", "barge_in", "new_reply",
    "device_error"}`.
  - `normalize_reason(reason, *, by_user)` — unknown by_user → `"user_stop"`,
    unknown non-user → `"device_error"`. Никаких raise.
  - `clamp_speech_progress(value)` — `None` или float в [0.0, 1.0],
    NaN / non-numeric → `None`.
  - `PlaybackInterruption` (frozen): `reason`, `by_user`,
    `source_reply_id`, `speech_progress`, `reaction_hint`.
  - `compute_reaction_hint(*, reason, by_user, interruption_count_recent,
    speech_progress=None)` — pure, bounded:
    - `new_reply` / `device_error` → всегда `"none"`;
    - `user_stop` ladder: 1 → `"reserved"`, 2 → `"comfort"`, 3+ → `"hurt"`
      (saturating);
    - `barge_in` ladder: 1-2 → `"none"`, 3+ → `"reserved"`;
    - `speech_progress < 0.1` — early-cut, ступенчатый downshift
      (cut → менее болезненно).
  - Гарантии: **никаких affection/trust мутаций**, никаких LLM-вызовов,
    никаких runtime events. Просто строка.

- **`core/voice/playback.py`** (новый):
  - `PlaybackState` (mutable dataclass): `active`, `provider`,
    `current_audio_path`, `source_reply_id`, `started_at`,
    `interrupted_at`, `finished_at`, `interruption_count_recent`,
    `last_interruption_reason`. `snapshot()` + `to_dict()` — safe для
    debug.
  - `PlaybackController`:
    - `start(*, provider, audio_path, source_reply_id, request_id,
      conversation_id)` — если активно, сначала эмитит
      `new_reply` interruption (контролируемая смена track'а), потом
      эмитит `playback_started`.
    - `finish(*, reason="completed", ...)` — эмитит `playback_finished`;
      идемпотентен (повторный no-op).
    - `interrupt(*, reason, by_user, source_reply_id, speech_progress,
      ...)` — no-op если не активно. Всегда нормализует reason через
      `normalize_reason`, инкрементирует `interruption_count_recent`,
      эмитит `playback_interrupted`, и при `by_user=True` дополнительно
      `playback_interrupted_by_user` с `reaction_hint`. Возвращает
      `PlaybackInterruption`.
    - `decay_recent_interruptions()` — обнуляет recent counter +
      last reason. Время / timer внутри controller'а нет (caller-
      driven), чтобы тесты оставались детерминированными.
    - `is_active()` / `state` — read-only snapshot.
  - **State runtime-only**: никакого JSON, никаких persistent файлов.
  - **НЕ владеет MariaBrain**, **НЕ импортирует runtime lock**,
    **НЕ трогает affection/trust/romance state**. Контрактный тест
    `test_controller_does_not_import_maria_brain_or_runtime_lock`
    проверяет границу.

Changed:

- **`core/voice/manager.py`**:
  - 3 новых event-константы: `EVENT_PLAYBACK_STARTED`,
    `EVENT_PLAYBACK_FINISHED`, `EVENT_PLAYBACK_INTERRUPTED_BY_USER`.
    `VOICE_EVENT_TYPES` теперь 10 элементов.
  - emit helpers: `emit_playback_started`, `emit_playback_finished`,
    `emit_playback_interrupted_by_user`. Payload — только safe
    metadata (provider, source_reply_id, reason, speech_progress
    (rounded), reaction_hint). Никакого raw text / thought /
    audio bytes.
  - `VoiceManager.__init__(..., playback_controller=None)`.
  - `mark_playback_started(*, provider, audio_path, source_reply_id, …)` —
    с контроллером делегирует и возвращает `PlaybackState`; без
    контроллера — legacy boolean flag (backward-compat).
  - `mark_playback_finished(*, reason="completed", …)` — новый метод.
  - `mark_playback_interrupted(*, reason="manual", by_user=False,
    source_reply_id=None, speech_progress=None, …)` — сигнатура
    расширена. С контроллером возвращает `PlaybackInterruption`. Без
    контроллера сохраняет старое поведение (один dict event), но
    дополнительно: `manual + by_user=True` → reason нормализуется в
    `"user_stop"` и эмитится `playback_interrupted_by_user`;
    unknown non-user reason → `"device_error"`.
  - `current_state().playback_active` теперь учитывает controller
    (если задан) либо legacy flag.

- **`core/voice/__init__.py`** — реэкспорт `PlaybackController`,
  `PlaybackState`, `PlaybackInterruption`, `INTERRUPTION_REASONS`,
  `compute_reaction_hint`, `normalize_reason`, новых event-констант.

Tests (29 новых):

- `tests/test_playback_controller.py` (16): event types set
  расширен; controller не импортирует brain/lock; start mark'ает
  active и эмитит `playback_started` с правильным payload; finish
  emits и идемпотентен; interrupt при неактивном — no-op без events;
  `user_stop+by_user=True` эмитит **оба** interrupted events с
  reaction_hint; non-user interruption эмитит только
  `playback_interrupted`; repeated interruptions инкрементируют
  counter; `decay_recent_interruptions()` обнуляет counter и last
  reason; `start` поверх active автоматически эмитит `new_reply`
  interruption; event payloads без forbidden keys
  (`thought`/`raw_thought`/`system_prompt`/`memories`/`api_key`/
  `token`/`secret`/`password`/`raw_text`); `VoiceManager(
  playback_controller=ctrl)` делегирует state контроллеру;
  без контроллера — legacy flag-flow остаётся как было;
  `manual+by_user=True` → reason нормализуется в `user_stop`;
  unknown non-user reason → `device_error`; `PlaybackController.__init__`
  не принимает `brain`/`maria_brain`/`lock` параметры.
- `tests/test_voice_interruption_effects.py` (13): `INTERRUPTION_REASONS`
  set; `normalize_reason` unknown non-user → `device_error`;
  manual + by_user → `user_stop`; `clamp_speech_progress` границы +
  NaN/non-numeric; user_stop ladder (1→reserved, 2→comfort, 3+→hurt,
  saturating); barge_in ladder (1-2→none, 3+→reserved); new_reply /
  device_error всегда `none`; early-cut downshift; unknown reason
  не падает и возвращает `none`; `compute_reaction_hint` pure;
  `PlaybackInterruption` без thought-полей в asdict; модуль не
  импортирует `core.engine.brain` или `core.runtime.lock`.

Также обновлён `tests/test_voice_manager.py::test_voice_event_types_*`
— expected set расширен до 10.

Намеренно НЕ тронуто:
- `TurnExecutor`, payload `/api/chat`, lock model. PlaybackController
  никем не вызывается из turn'а — он foundation для real audio device
  wrapper / Unity bridge / debug API.
- `chat_pipeline as cp` seam.
- `core/personas/maria.py`.
- Affection / trust / romance / breakup state — single user_stop не
  должен наказывать (per spec). Repeated → `hurt` остаётся только
  *подсказкой*, а не мутацией.
- Реальное audio-устройство (PyAudio / sounddevice). Controller это
  логическая модель; sound output подключим, когда понадобится Unity
  bridge или local playback.
- STT / microphone — `0.20.4.4`.
- Persistent state: `interruption_count_recent` runtime-only, как
  и было оговорено.

Validation:
```bash
MariEnv\Scripts\python.exe -m compileall -q .
MariEnv\Scripts\pytest.exe tests/test_playback_controller.py \
       tests/test_voice_interruption_effects.py -q
MariEnv\Scripts\pytest.exe -q
```

Result:
- compileall: OK
- targeted (new tests): **29 passed**
- full suite: **1156 passed** (was 1127, +29).

Risks / limits:
- `compute_reaction_hint` пока никем не потребляется. Director /
  offline-cognition consumer этой подсказки появится позже —
  тесты покрывают только pure-function поведение.
- `interruption_count_recent` сбрасывается ТОЛЬКО через явный
  `decay_recent_interruptions()`. Без вызова счётчик копится в
  рамках процесса. Это сознательно: time-based decay должен сидеть
  в offline-path / director, не в controller'е.
- `VoiceManager.mark_playback_interrupted` сохраняет backward-compat:
  возвращает dict если controller'а нет, возвращает
  `PlaybackInterruption` если есть. Callers, проверяющие тип,
  должны учитывать это; легаси-тесты остались зелёными.
- Реальное audio API не подключено. Когда появится — controller
  будет single source of truth для playback state.
- При `start()` поверх активной сессии controller автоматически
  эмитит `new_reply` interruption. Это решает silent-loss-of-state
  проблему, но добавляет два event'а вместо одного — каллеры, которые
  считают events, должны это учитывать.

Next recommended:
- `0.20.4.4` — VAD-based STT Provider Pipeline.

---

## 0.20.4.3 (mini-patch) — Audio Device Config

Goal:
По запросу — вынести физические audio-устройства (вход/выход) в
config, чтобы будущий real audio I/O wrapper (STT capture, playback
sink, Unity bridge) читал их из одного места, без хардкода. Сам
аудио-стек не подключён — это только generic config carrier.

Added:
- **`config.py`**:
  - `VOICE_AUDIO_OUTPUT_DEVICE = None`
  - `VOICE_AUDIO_INPUT_DEVICE = None`
  - `VOICE_AUDIO_INPUT_SAMPLE_RATE = 16_000` (Silero VAD дефолт)
  - `VOICE_AUDIO_INPUT_CHANNELS = 1` (mono)
  - Semantics — sounddevice convention: `None` → system default,
    `int` → device index, `str` → substring of device name.
- **`core/voice/audio_devices.py`** (новый):
  - `DeviceSelector` type alias.
  - `normalize_device(value)`: `None` / `int` / `str` passthrough,
    bool / float / object → `None`, пустые / whitespace-only строки
    → `None`. Никаких raise.
  - `AudioDeviceConfig` (frozen): `output_device`, `input_device`,
    `input_sample_rate`, `input_channels`.
  - `load_audio_device_config()` — лениво читает `config`, clamps
    sample rate в [8000, 96000], channels в [1, 2]. Не открывает
    аудио-устройства и не импортирует `sounddevice`/`pyaudio`.
- **`core/voice/__init__.py`** — реэкспорт публичных типов.

Tests (11 новых, `tests/test_audio_device_config.py`):
- `normalize_device` passthrough / strip / empty-to-None / reject
  bool & float;
- `load_audio_device_config()` defaults при пустом config'е;
- overrides читаются (int output, str input);
- sample_rate clamp к [8000, 96000];
- channels clamp к [1, 2];
- инвалидный sample_rate → default 16_000;
- `AudioDeviceConfig` frozen;
- module не подгружает `sounddevice` / `pyaudio` при импорте.

Намеренно НЕ тронуто:
- Реальный audio I/O (sounddevice / PyAudio / Unity audio bridge).
  Это foundation; consumer'ы — `0.20.4.4` STT и будущий playback
  wrapper.
- `core/voice/tts_service.py` / `core/voice/playback.py` / `manager.py`
  — пока не дёргают `load_audio_device_config()`. Дёрнут когда
  понадобится реальное устройство.
- `requirements*.txt` — sounddevice / pyaudio добавим только когда
  реальный wrapper появится.

Validation:
- compileall: OK
- targeted: **11 passed**
- full suite: **1167 passed** (was 1156, +11).

---

## 0.20.4.4 — VAD-based STT Provider Pipeline

Goal:
Подключить optional STT pipeline над уже существующим VAD-сегментатором:
audio segment → safe transcript → safe runtime events. Raw audio
никогда не персистится по дефолту, transcript НЕ авто-сабмитится в
MariaBrain без явного opt-in. Никаких heavy STT deps в pytest.

Added:

- **`core/voice/stt_faster_whisper.py`** (новый):
  - `FasterWhisperSTTProvider(model_size, language, device, compute_type)`.
  - Lazy import `faster_whisper` / `ctranslate2` через `_load()`. При
    отсутствии deps возвращает `STTResult(ok=False, error="faster_whisper
    not installed: …")` вместо raise.
  - Уверенность вычисляется как нормализованное среднее `avg_logprob`
    (clamp в [0, 1]). При пустом transcript → `ok=False`.

- **`core/voice/stt_service.py`** (новый):
  - `STTService(provider, language="ru", min_confidence=0.45)`:
    `min_confidence` clamp'ится в [0, 1].
  - `transcribe(segment, *, request_id, conversation_id)`:
    - Emit `stt_segment_started` (provider, duration_sec, sample_rate).
    - Вызывает `provider.transcribe_segment(segment)` под try/except.
    - Успех → `stt_segment_completed` (text_length, confidence,
      language). При `confidence < min_confidence` дополнительно
      `stt_low_confidence` с reason `below_min_confidence`.
    - Неуспех → `stt_failed` с reason.
    - **Payload не содержит самого текста transcript'а** — только
      length. Сам transcript остаётся в return value, в runtime sink
      не пишется.
  - `build_stt_service(name, *, language, min_confidence, **kwargs)` —
    builder для `disabled` / `fake` / `faster_whisper` / `vosk`
    (последний пока placeholder — возвращает Disabled).
  - `build_stt_service_from_config()` — читает `VOICE_STT_*` config,
    при `VOICE_STT_ENABLED=False` всегда Disabled.

- **`core/voice/stt_intake.py`** (новый):
  - `should_auto_submit_transcript(*, transcript, confidence,
    voice_enabled, stt_enabled, auto_submit_enabled, min_confidence)`
    → `(allow, reason)`. Стабильные reason'ы: `voice_disabled`,
    `stt_disabled`, `auto_submit_disabled`, `empty_transcript`,
    `missing_confidence`, `low_confidence`, `ok`.
  - Не интегрирован в TurnExecutor / `MariaBrain.handle_message`.
    Foundation для будущего mic flow / debug API.

Changed:

- **`core/voice/stt_base.py`**:
  - `STTResult.confidence: float = 0.0` — новое поле. Backward-compatible:
    существующие caller'ы / `VoiceManager.transcribe()` уже читали через
    `getattr(..., 'confidence', 0.0)`.
  - `DisabledSTTProvider` явно возвращает `confidence=0.0`.
  - `FakeSTTProvider` (новый): детерминированный transcribe для
    тестов, конфигурируемый `text` / `confidence` / `ok` / `error`.

- **`core/voice/listener.py`**:
  - Импорт `VADConfig` добавлен.
  - `build_voice_listener_from_config()` — фабрика над config'ом.
    Возвращает `None` если voice / stt отключены, или если
    выбранный provider = `disabled`. Иначе собирает `EnergyVADSegmenter`
    + STT provider в `VADVoiceListener`. Не открывает аудио-устройства,
    не подгружает faster_whisper (lazy).

- **`core/voice/manager.py`**:
  - 3 новых event-константы: `EVENT_STT_SEGMENT_STARTED`,
    `EVENT_STT_SEGMENT_COMPLETED`, `EVENT_STT_FAILED`. `VOICE_EVENT_TYPES`
    теперь 13 элементов.
  - emit helpers: `emit_stt_segment_started`,
    `emit_stt_segment_completed`, `emit_stt_failed`. Все payload'ы
    содержат только safe metadata (provider, duration_sec, sample_rate,
    text_length, confidence, language, reason). НЕТ полей `text` /
    `transcript` / `raw_audio` / `samples`.
  - `VoiceManager.transcribe()` теперь читает порог
    `config.VOICE_STT_MIN_CONFIDENCE` (fallback 0.4). Не меняет
    return shape — только threshold.

- **`config.py`** — 7 новых флагов:
  ```
  VOICE_STT_ENABLED = False
  VOICE_STT_PROVIDER = "disabled"
  VOICE_STT_LANGUAGE = "ru"
  VOICE_STT_MIN_CONFIDENCE = 0.45
  VOICE_STT_AUTO_SUBMIT = False
  VOICE_STT_STORE_RAW_AUDIO = False
  VOICE_STT_STORE_TRANSCRIPTS = False
  ```
  Все off-by-default. Raw audio и transcript persistence требуют
  явного opt-in через config; на этом stage никто их не читает —
  они есть как explicit-opt-in surfaces.

- **`core/voice/__init__.py`** — реэкспорт `STTService`,
  `FakeSTTProvider`, `build_stt_service*`, `should_auto_submit_transcript`,
  `build_voice_listener_from_config`, новых event-констант.

Tests (30 новых):

- `tests/test_stt_provider_pipeline.py` (11): `DisabledSTTProvider` →
  ok=False с confidence=0; `FakeSTTProvider` возвращает заданный
  text / confidence; `build_stt_service("faster_whisper")` не
  подгружает `faster_whisper`; faster_whisper provider возвращает
  error при отсутствии lib; `STTService.transcribe()` ok → emits
  segment_started + segment_completed с правильным payload'ом;
  ok=False → emits stt_failed; ok=True но низкий confidence →
  дополнительно stt_low_confidence; event payload без `text` /
  `transcript` / `raw_audio` / `samples` / thought-полей;
  `build_stt_service_from_config()` уважает `VOICE_STT_ENABLED=False`;
  с включённым `fake` provider'ом подхватывает language / min_confidence;
  min_confidence clamp в [0, 1].
- `tests/test_vad_listener_integration.py` (9): silent chunks → пусто;
  speech segment + silence → одна VoiceUtterance с правильным text;
  listener не пишет файлы на диск (chdir в tmp_path, assert empty);
  `build_voice_listener_from_config()` возвращает `None` при STT
  off / voice off / provider=disabled; с `fake` provider'ом — usable
  listener; не подгружает `faster_whisper` через factory; STT с
  ok=False → utterance НЕ эмитится.
- `tests/test_stt_intake_gate.py` (10): signature без
  `thought`/`raw_audio`/`samples`; reason codes
  `voice_disabled`/`stt_disabled`/`auto_submit_disabled`/`empty_transcript`/
  `missing_confidence`/`low_confidence`; allow при всех включённых +
  достаточный confidence; чтение config-флагов; sanity check, что
  `VOICE_STT_AUTO_SUBMIT` / `VOICE_STT_STORE_RAW_AUDIO` /
  `VOICE_STT_STORE_TRANSCRIPTS` все по умолчанию `False`.

Также обновлён `tests/test_voice_manager.py::test_voice_event_types_*`
— expected set расширен до 13.

Намеренно НЕ тронуто:
- `TurnExecutor` / `handle_message` / payload `/api/chat` — никто
  не зовёт `should_auto_submit_transcript` из turn'а. Включение
  mic-driven flow ждёт debug API stage (`0.20.4.5`) / Unity bridge
  (`0.20.4.6`).
- `chat_pipeline as cp` seam.
- `core/personas/maria.py`, `data/persona.md`.
- Реальное audio-устройство — `core/voice/audio_devices.py` появился
  как config carrier ранее, но всё ещё не подключён к real I/O.
- Persistent storage: `data/voice/transcripts/` / `data/voice/raw/`
  не создаются. Если флаги когда-то включат, тогда добавим
  retention / gitignore (data/** уже gitignored).
- Wake word, continuous always-on mic, voice biometrics.
- LLM-вызовы / `core/llm.py` — STT никак с LLM не пересекается.

Validation:
```bash
MariEnv\Scripts\python.exe -m compileall -q .
MariEnv\Scripts\pytest.exe tests/test_stt_provider_pipeline.py \
       tests/test_vad_listener_integration.py \
       tests/test_stt_intake_gate.py -q
MariEnv\Scripts\pytest.exe -q
```

Result:
- compileall: OK
- targeted (new tests): **30 passed**
- full suite: **1197 passed** (was 1167, +30).

Risks / limits:
- `FasterWhisperSTTProvider.transcribe_segment` сейчас передаёт
  `array.array("f", segment.samples)` напрямую в `WhisperModel.transcribe`.
  Реальный faster_whisper ожидает `np.ndarray` либо path к файлу. Эта
  ветка покрыта `# pragma: no cover` — если кто-то когда-то реально
  включит faster_whisper, нужна будет numpy-конверсия и audio resample
  до 16k. Сейчас это placeholder: главное — что отсутствие deps не
  ломает import / pytest.
- Threshold `0.4 → 0.45` в `VoiceManager.transcribe()`: в существующем
  тесте `test_transcribe_high_confidence_no_event` confidence=0.9
  (выше обоих), `test_transcribe_low_confidence_emits_event`
  confidence=0.2 (ниже обоих) — оба проходят.
- `STTService` имеет собственный pipeline events / threshold, а
  `VoiceManager.transcribe()` — свой (legacy). Дублирование намеренно:
  Manager.transcribe() сохраняет старую сигнатуру (для
  `tests/test_voice_manager.py`), STTService — новый высокоуровневый
  путь. В будущем (`0.20.4.5+`) их можно консолидировать, но это
  меняло бы public contract VoiceManager'а.
- `text` поля в payload runtime event'ов нет (privacy). Caller
  получает полный transcript в return value — это его
  ответственность, что с ним делать (sanitised log, в-memory, etc).
- Persistent storage флаги (`VOICE_STT_STORE_RAW_AUDIO`,
  `VOICE_STT_STORE_TRANSCRIPTS`) пока никем не читаются. Это
  будущая отдельная задача с явной retention policy.

Next recommended:
- `0.20.4.5a` — Voice package layout split (organisational; precedes
  debug endpoints per the layout spec).
- `0.20.4.5b` — Backend / WebUI Voice Debug Endpoints. Здесь же
  появятся первые публичные `/api/webui/voice/*` или
  `/api/voice/debug/*` с явными payload-shape'ами.

---

## 0.20.4.5a — Voice package layout split

Goal:
Перенести `core/voice/` из плоского god-package'а в responsibility-
based подпакеты ДО внедрения debug endpoints. Поведенческих изменений
нет — это организационный патч. Старые flat-импорты продолжают работать
через тонкие re-export shim'ы.

Per voice layout boundary spec
(`docs/old_roadmaps/voice_module_layout_split.md`).

New layout:

```
core/voice/
  __init__.py                      ← public re-exports (canonical paths)
  types.py                         ← shared types (unchanged)
  audio_devices.py                 ← shared config carrier (unchanged)
  manager.py                       ← VoiceManager facade (unchanged)

  tts/
    __init__.py
    base.py           silero.py           pyttsx3.py
    service.py        text.py             cache.py

  stt/
    __init__.py
    base.py           faster_whisper.py
    service.py        intake.py

  vad/
    __init__.py
    segmenter.py      listener.py         microphone.py

  playback/
    __init__.py
    controller.py     interruption.py

  orchestration/
    __init__.py
    reaction.py       speech_plan.py
    orchestrator.py   auto_speak.py
```

Backward-compat shims (each is `from <new.path> import *  # noqa`):

```
core/voice/tts_base.py             → core.voice.tts.base
core/voice/tts_service.py          → core.voice.tts.service
core/voice/tts_text.py             → core.voice.tts.text
core/voice/tts_silero.py           → core.voice.tts.silero
core/voice/tts_pyttsx3.py          → core.voice.tts.pyttsx3
core/voice/cache.py                → core.voice.tts.cache
core/voice/stt_base.py             → core.voice.stt.base
core/voice/stt_service.py          → core.voice.stt.service
core/voice/stt_faster_whisper.py   → core.voice.stt.faster_whisper
core/voice/stt_intake.py           → core.voice.stt.intake
core/voice/listener.py             → core.voice.vad.listener
core/voice/microphone.py           → core.voice.vad.microphone
core/voice/interruption.py         → core.voice.playback.interruption
core/voice/reaction.py             → core.voice.orchestration.reaction
core/voice/speech_plan.py          → core.voice.orchestration.speech_plan
core/voice/orchestrator.py         → core.voice.orchestration.orchestrator
core/voice/auto_speak.py           → core.voice.orchestration.auto_speak
```

Removed flat files (имена коллидируют с новыми пакетами — теперь
обслуживаются `__init__.py` пакетов):
- `core/voice/vad.py`        (содержимое: `core/voice/vad/segmenter.py`)
- `core/voice/playback.py`   (содержимое: `core/voice/playback/controller.py`)

Внутренние imports внутри moved-файлов переписаны на канонические
package-paths (`core.voice.tts.base` вместо `core.voice.tts_base` и т.д.).
Top-level `core/voice/__init__.py` теперь импортирует исключительно
из subpackages (через их `__init__.py`).

Boundary rules (закреплены тестом):

- TTS не импортирует STT / VAD / Playback.
- STT не импортирует TTS / Playback.
- VAD не импортирует TTS / Playback / brain / personas.
- Playback не импортирует brain / runtime.lock / personas / другие
  voice subpackages.
- Orchestration не импортирует personas / реальные провайдеры
  (silero / pyttsx3 / faster_whisper / stt.service). Используется
  только `tts.text` для visible-reply preprocessing — это allowed
  per layout spec §3.6.

Tests (9 новых, `tests/test_voice_module_layout.py`):
- Все 19 новых package-path импортов резолвятся.
- Все 17 старых flat-path импортов резолвятся в **тот же** объект
  что и новые (`is`-identity check через `getattr`).
- `core.voice.vad` и `core.voice.playback` теперь package-import'ы,
  но публичные имена (`EnergyVADSegmenter`, `VADVoiceListener`,
  `PlaybackController`, `PlaybackInterruption`) доступны через
  package `__init__.py`.
- Контрактные guards на boundary rules — static source-string проверка
  для каждого подпакета (TTS / STT / VAD / Playback / Orchestration).
- `core.voice` top-level всё ещё экспортирует ключевые имена.

Намеренно НЕ тронуто:
- Поведение TTS / STT / VAD / Playback / VoiceManager — байт-в-байт
  то же (все 1197 предыдущих тестов остались зелёными после
  миграции, прежде чем добавился новый architecture test).
- VoiceManager public API.
- Runtime events / event types / payload shape.
- Config names / values.
- TurnExecutor, `chat_pipeline`, persona.
- Real audio device access (по-прежнему нет).
- Shims **не удаляются** — оставлены минимум на один stable tag,
  per spec §4 Stage C.

Validation:
```bash
MariEnv\Scripts\python.exe -m compileall -q .
MariEnv\Scripts\pytest.exe tests/test_voice_module_layout.py -q
MariEnv\Scripts\pytest.exe -q
```

Result:
- compileall: OK
- targeted (new architecture tests): **9 passed**
- full suite: **1206 passed** (was 1197, +9). Никаких регрессий.

Risks / limits:
- Shims живут как `from new.path import *  # noqa: F401,F403`. Если
  где-то импортируется приватное имя из старого модуля (с
  underscore-префиксом), оно не реэкспортируется через star-import.
  Контрактный тест проверяет identity только публичных имён.
- `core/voice/vad.py` и `core/voice/playback.py` удалены — потенциально
  ломающий момент, если кто-то снаружи делал `from core.voice import
  vad` или `from core.voice import playback` (rare); сейчас это
  package-import и работает аналогично.
- Static source-string проверка на boundary rules ищет подстроку
  `core.voice.X` в исходниках; если кто-то когда-нибудь напишет
  `importlib.import_module("core.voice.stt.service")` внутри TTS-кода,
  guard не сработает. Это слабое место, но достаточно для catch'а
  обычных `from core.voice.X import …` нарушений.

Next recommended:
- `0.20.4.5b` — Backend / WebUI Voice Debug Endpoints. Теперь
  endpoint-код может импортировать через канонические package-paths
  (например, `from core.voice.tts.cache import cleanup_voice_cache`,
  `from core.voice.orchestration.orchestrator import SpeechOrchestrator`).

---

## 0.20.4.5b — Backend / WebUI Voice Debug Endpoints

Goal:
Open the first **public local-only** HTTP surface over voice runtime
для smoke checks и будущей WebUI debug-панели: `/voice/state`,
`/voice/speak`, `/voice/interrupt`, `/voice/cache/cleanup`,
`/voice/events`, `/stt/transcribe`. Backend-only; никакого UI,
никакого Unity bridge'а на этом stage'е.

Per spec `MARIA_0_20_4_VOICE_RUNTIME_ROADMAP.md` §Stage 0.20.4.5,
с учётом layout split из 0.20.4.5a (импорты через canonical package
paths — `from core.voice.tts.cache import …`, `from core.voice.
orchestration.orchestrator import …`).

Added:

- **`apps/voice_debug_api.py`** (новый):
  - `create_voice_debug_api_blueprint(name)` — Flask Blueprint
    factory с шестью endpoint'ами.
  - Module-level test seams: `_get_voice_manager()`,
    `_get_orchestrator()`, `_get_stt_service()`,
    `_get_playback_controller()`, `_get_cache_dir()` —
    monkeypatch-friendly attribute hooks (тесты подменяют
    через `monkeypatch.setattr(voice_api, "_get_…", lambda: …)`).
  - `_strip()` рекурсивно дропает forbidden keys (`thought`,
    `raw_thought`, `system_prompt`, `memories`, `facts`,
    `api_key`, `secret`, `password`, `token`, `samples`,
    `raw_audio`, `audio_bytes`) из ответа.
  - `_cache_stats()` считает `*.wav` файлы и MB без чтения
    содержимого.
  - `_build_segment()` строит `SpeechSegment` из JSON-payload
    (`samples: [float]` или `duration_sec: float`); пустой
    payload → `(None, "missing_audio")` → 400.

- **`apps/web_debug_app.py`** — blueprint регистрируется **дважды**:
  - `/api/webui/voice/*` + `/api/webui/stt/*` — primary
    WebUI debug surface.
  - `/api/voice/*` + `/api/stt/*` — client-core alias.

Endpoint contracts:

```
GET  /api/webui/voice/state
     → {tts_enabled, stt_enabled, tts_provider, stt_provider,
        playback_active, cache: {dir, files, size_mb, exists},
        playback?: {active, provider, source_reply_id, …}}

POST /api/webui/voice/speak
     body: {text, emotion?, voice?, request_id?}
     → {ok, provider, audio_path, cached, reason,
        speech_plan: {speed_multiplier, volume_multiplier,
                      provider_hint, voice, style_tags,
                      segment_count}}
     400 если text пуст.

POST /api/webui/voice/interrupt
     body: {reason?, by_user?, source_reply_id?, speech_progress?}
     → {ok, was_active, reason, by_user, reaction_hint,
        speech_progress, interruption_count_recent?}

POST /api/webui/voice/cache/cleanup
     body: {max_files?, max_mb?, max_age_hours?} (опционально,
            иначе config)
     → {ok, cache_dir, removed, kept, bytes_removed, skipped}

GET  /api/webui/voice/events?limit=50
     → {ok, events: [...]<=200, count}
     events отфильтрованы по `event_type ∈ VOICE_EVENT_TYPES`.

POST /api/webui/stt/transcribe
     body: {samples?: [float], sample_rate?, duration_sec?,
            request_id?, conversation_id?}
     → {ok, text, confidence, language, provider, reason}
     400 missing_audio если нет ни samples, ни duration_sec.
```

Security / privacy:
- Все response payload'ы проходят через `_strip()` — forbidden keys
  (thought, raw_thought, system_prompt, memories, api_key, token,
  secret, password, raw_audio, samples, audio_bytes) гарантированно
  не утекают.
- `/stt/transcribe` принимает `samples` как JSON-список floats —
  endpoint **не** пишет их на диск, не сохраняет в memory. Это
  doc'd как "debug-only / local trusted use".
- `/voice/speak` возвращает `audio_path` как локальный путь, но
  файл через HTTP **не** отдаётся. Скачивание wav через HTTP — не
  входит в scope этого stage'а (per spec non-goal: «не expose raw
  audio files outside local trusted use»).
- Endpoint работает только на `127.0.0.1` (через
  `config.WEB_HOST`) с локальным CORS-allowlist'ом — без
  аутентификации, но и без внешнего доступа.

Tests (16 новых, `tests/test_voice_debug_api.py`):
- `_reset_voice` autouse fixture: подменяет `_get_voice_manager`,
  `_get_playback_controller`, `_get_cache_dir`, `_get_stt_service`
  на fake-ы для каждого теста; `clear_runtime_events()`.
- `/voice/state` — safe shape, alias `/api/voice/state`.
- `/voice/speak` — empty text → 400; strips `*action*`/эмодзи перед
  синтезом (контрактный тест: provider получает чистый text); ok
  payload содержит `speech_plan.speed_multiplier` / `voice` /
  `style_tags`; alias `/api/voice/speak`.
- `/voice/interrupt` — user_stop+by_user=True эмитит и
  `playback_interrupted`, и `playback_interrupted_by_user`, и
  возвращает `reaction_hint`; interrupt при неактивном playback
  — safe, `was_active=False`.
- `/voice/cache/cleanup` — удаляет лишние файлы; missing cache dir
  → `skipped=True`, `removed=0`.
- `/voice/events` — содержит только `event_type ∈ VOICE_EVENT_TYPES`,
  `limit` clamp'ится к 200; payload без forbidden keys.
- `/stt/transcribe` — empty body → 400 missing_audio; samples
  + sample_rate → ok payload с transcript из fake provider;
  только duration_sec тоже работает; alias `/api/stt/transcribe`.
- Sanity: ни один из 5 endpoint'ов не утекает forbidden keys
  в response payload.

Намеренно НЕ тронуто:
- Frontend / React WebUI — нет.
- Unity bridge — `0.20.4.6`.
- WebSocket streaming — нет.
- Передача audio bytes через HTTP — нет (только path).
- Auto-submit STT transcript в `MariaBrain.handle_message` — нет.
  Endpoint только транскрибирует, не сабмитит.
- `TurnExecutor` / `chat_pipeline` / payload `/api/chat` — никакой
  voice debug endpoint не модифицирует обычный chat-flow.
- Реальное audio device wrapping — `audio_devices.py` carrier
  остаётся не подключённым.
- Persistent storage — никакие raw audio / transcripts не пишутся
  на диск этими endpoint'ами.

Validation:
```bash
MariEnv\Scripts\python.exe -m compileall -q .
MariEnv\Scripts\pytest.exe tests/test_voice_debug_api.py -q
MariEnv\Scripts\pytest.exe -q
```

Result:
- compileall: OK
- targeted (new tests): **16 passed**
- full suite: **1222 passed** (was 1206, +16).

Risks / limits:
- Module-level seams (`_get_voice_manager()` etc.) — отлично для
  тестов, но в production они возвращают global brain.voice. Если
  кто-то рестартит `reset_default_brain()` в середине запроса —
  seam перечитает; race-condition теоретическая, на одном клиенте
  не опасна.
- `/voice/speak` использует `SpeechOrchestrator()` без profile
  (neutral). Когда `core/personas/maria_voice.py` будет создан и
  подключён, нужно будет передать профиль в `_get_orchestrator()`
  — отдельная задача.
- `/voice/events` post-filter'ит из `recent_runtime_events(limit*4)`.
  Если voice events очень редки на фоне многих не-voice, можно
  пропустить старые voice-event'ы. На дефолтных лимитах (50 max,
  pull 200) это нерелевантно.
- `/stt/transcribe` принимает `samples` напрямую JSON-листом — для
  больших аудио (десятки секунд при 16kHz) payload будет тяжёлым.
  Документировано как "debug-only". Для production-mic flow в
  0.20.4.6 Unity bridge'е надо будет либо multipart upload, либо
  отдельный binary endpoint.

Next recommended:
- `0.20.4.6` — Voice Manual Scenario Campaign / Unity bridge.

---

## 0.20.4.5c — Voice flat-import cleanup

Goal:
After the layout split (`0.20.4.5a`) and debug endpoints (`0.20.4.5b`),
internal backend code was still importing voice modules through the
legacy flat paths (`core.voice.tts_service`, `core.voice.reaction`,
etc.) via the compatibility shims. This stage migrates every internal
import to the canonical package paths and locks the boundary with a
regression test. Behaviourally a no-op.

Constraints honoured per stage spec:
- backend-only;
- no behaviour changes — same runtime events, same configs, same
  endpoints;
- no real audio device access;
- no private persona imports;
- shims kept (only renamed-internal references migrated).

Changed:

- **`core/voice/manager.py`**: lazy import in
  `mark_playback_interrupted()` now uses
  `core.voice.playback.interruption` instead of the legacy
  `core.voice.interruption` shim.
- **`core/voice/__init__.py`**: docstring example clarified (shim
  examples explicitly labelled "historical flat module paths"). All
  imports already used package paths from `0.20.4.5a`.
- **`tools/voice_tts_debug.py`**: imports now resolve through
  `core.voice.tts.service` / `core.voice.tts.text`.
- **Tests** (no behaviour, just imports — 17 test files touched):
  - `test_0202_maria_tts_text.py`, `test_0202_voice_silero_config.py`,
    `test_0202_voice_tts.py`, `test_0204_tts_text_history_integration.py`,
    `test_playback_controller.py`, `test_speech_orchestrator.py`,
    `test_stt_intake_gate.py`, `test_stt_provider_pipeline.py`,
    `test_thought_visibility.py`, `test_tts_provider_pipeline.py`,
    `test_vad_listener_integration.py`, `test_voice_auto_speak.py`,
    `test_voice_cache_cleanup.py`, `test_voice_interruption_effects.py`,
    `test_voice_reaction_tags.py`, `test_voice_style_profile.py`.
  - All flat-path imports → canonical (`core.voice.tts.base`,
    `core.voice.tts.service`, `core.voice.tts.text`,
    `core.voice.tts.cache`, `core.voice.stt.base`,
    `core.voice.stt.service`, `core.voice.stt.intake`,
    `core.voice.vad.listener`, `core.voice.playback.interruption`,
    `core.voice.orchestration.reaction`,
    `core.voice.orchestration.speech_plan`,
    `core.voice.orchestration.orchestrator`,
    `core.voice.orchestration.auto_speak`).
  - `test_playback_controller.py::test_controller_does_not_import_
    maria_brain_or_runtime_lock` — package reference adjusted to
    `core.voice.playback.interruption` (the actual module).

Added (architecture guard):

- **`tests/test_voice_module_layout.py::test_no_production_code_
  imports_legacy_flat_voice_modules`** — walks `core/`, `apps/`,
  `tools/`, `tests/` and asserts no `from core.voice.<flat> …` /
  `import core.voice.<flat>` statements exist outside the shim files
  themselves and this very test (which still verifies the shims
  resolve in the identity-pair test above). Regex is import-line-
  specific so docstring / comment mentions of historical paths are
  fine.

Shims kept:
- `core/voice/{tts_base,tts_service,tts_text,tts_silero,tts_pyttsx3,
  cache,stt_base,stt_service,stt_faster_whisper,stt_intake,listener,
  microphone,interruption,reaction,speech_plan,orchestrator,
  auto_speak}.py` — all 17 still in tree as
  `from core.voice.<new.path> import *` re-exports. External code
  and the identity-pair architecture test continue to use them.

Validation:
```bash
MariEnv\Scripts\python.exe -m compileall -q .
MariEnv\Scripts\pytest.exe tests/test_voice_module_layout.py -q
MariEnv\Scripts\pytest.exe -q
```

Result:
- compileall: OK
- targeted (architecture tests now 10): **10 passed**
- full suite: **1223 passed** (was 1222, +1 = new guard). Никаких
  регрессий по поведению — все 16 voice debug endpoint тестов,
  все TTS / STT / VAD / playback / orchestration / cache /
  intake / auto-speak тесты остались зелёными байт-в-байт.

Risks / limits:
- Architecture guard работает по regex по строкам, начинающимся с
  `from core.voice.<flat>` / `import core.voice.<flat>`. Если кто-то
  однажды напишет `importlib.import_module("core.voice.tts_service")`
  внутри prod-кода, regex это пропустит. Сейчас этого нет, и оно
  заведомо плохой паттерн в самом коде — поэтому false negative
  принят сознательно.
- Shims не удаляются. Per layout spec §4 Stage C, удалять можно
  после: «tests no longer import old paths; docs updated; public
  API expectations updated; at least one stable tag after migration».
  Сейчас выполнены первые два пункта (тесты только через shim для
  identity-проверки; doc'ы / DEV_LOG не упоминают flat-paths как
  preferred). Удалять shim'ы — отдельный future stage.

Next recommended:
- `0.20.4.6` — Voice Manual Scenario Campaign / Unity bridge.

---

## 0.20.4.6 — Voice Manual Scenario Campaign

Goal:
Document the manual validation runbook for the voice runtime after
TTS/STT/playback/endpoints landed, and back the structural invariants
of that runbook with contract tests. Unity / Live2D bridge is **not**
in scope — explicitly postponed in the roadmap to 0.20.5+.

Added:

- **`docs/architecture/VOICE_MANUAL_SCENARIOS.md`** — operator runbook:
  - Preflight (config knobs, app launch).
  - `curl` shortcuts for `/api/webui/voice/state` /
    `/voice/speak` / `/voice/interrupt` / `/voice/cache/cleanup`
    / `/voice/events` / `/stt/transcribe`.
  - Six manual test groups: TTS text safety / emotion-state speech /
    auto-speak flag matrix / interruption ladder / STT-VAD privacy /
    runtime event sanitization.
  - 30-message smoke checklist with checkpoint annotations.
  - Cleanup recipe for the wav cache after a manual run.

- **`tests/test_voice_manual_scenario_contracts.py`** (27 tests) —
  automates the structural half of the runbook:
  - **Group 1 (TTS safety):** parametrised over five reference
    phrases — actions / env / code-fence / `~` / emoji never reach
    `spoken_text`; `...` normalises to `…`; trailing `)` survives.
  - **Group 2 (emotion-based):** 5 emotions yield distinct
    `SpeechPlan`s; `speed_multiplier` always within
    `[SPEED_MIN, SPEED_MAX]`; shy/tired slower than playful;
    shy phrase emits an `ellipsis` pause directive, playful one
    doesn't.
  - **Group 3 (auto-speak):** gate matrix (voice off / voice on
    + auto off / both on) → expected reason codes; manual speak
    works when auto is off; `maybe_auto_speak` invokes provider
    with the decorator-stripped text only.
  - **Group 4 (interruption):** single user_stop hint ∈
    {none, reserved, comfort} (never hurt on first stop); repeated
    saturates at "hurt" without further escalation; early cut
    (progress < 0.1) downshifts severity.
  - **Group 5 (STT intake):** parametrised gate matrix; `STTService`
    does not persist audio (chdir + rglob assertion); sanity that
    `VOICE_STT_AUTO_SUBMIT` / `_STORE_RAW_AUDIO` / `_STORE_TRANSCRIPTS`
    all default `False`.
  - **Group 6 (privacy):** every voice runtime event payload —
    speak, playback, STT lifecycle — checked for forbidden keys
    (`thought`, `raw_thought`, `system_prompt`, `memories`, `facts`,
    `api_key`, `token`, `secret`, `password`, `samples`, `raw_audio`,
    `audio_bytes`, `text`, `transcript`). Also asserts no brain
    state keys (`affection_state`, `breakup_state`, `rom_state`,
    etc.) leak through voice emit-helpers.

Намеренно НЕ тронуто:
- Реальные Силеро / faster-whisper / sounddevice / Unity.
- `TurnExecutor` / `chat_pipeline` / `/api/chat` payload.
- Endpoint contracts — runbook их только использует.
- Persona / `data/persona.md`.
- Runtime event types / sanitizer — без изменений.
- Lock model.
- Unity / Live2D bridge — отдельная веха (0.20.5+).

Validation:
```bash
MariEnv\Scripts\python.exe -m compileall -q .
MariEnv\Scripts\pytest.exe tests/test_voice_manual_scenario_contracts.py -q
MariEnv\Scripts\pytest.exe -q
```

Result:
- compileall: OK
- targeted: **27 passed**
- full suite: **1250 passed** (was 1223, +27).

Risks / limits:
- Runbook включает ручную "слуховую" проверку Силеро — это
  принципиально вне CI. Контрактные тесты покрывают structure
  (стрип, gate, ladder, privacy) — не aesthetic prosody.
- Interruption ladder test использует `compute_reaction_hint`
  напрямую, не через LLM-director — это намеренно (pure-function
  policy). Brain-side (как director использует hint) — отдельный
  слой, его автоматизация требует LLM, что non-goal для voice stage.

Voice runtime line (0.20.4.x) — **feature-complete**:
- 0.20.4.0a–g: TurnExecutor / VoiceManager contracts / TTS text
  preprocessing / lock design / RU doc refresh / humor delayed
  resolution / regen continuity guard.
- 0.20.4.1:  Voice reaction tags + SpeechOrchestrator foundation.
- 0.20.4.2:  Silero TTS pipeline + cache cleanup + auto-speak gate.
- 0.20.4.3:  Playback / interruption controller (+ audio device
              config mini-patch).
- 0.20.4.4:  VAD-based STT provider pipeline + intake gate.
- 0.20.4.5a: Voice package layout split (tts / stt / vad / playback /
              orchestration subpackages + compat shims).
- 0.20.4.5b: Voice debug endpoints (/voice/state, /voice/speak,
              /voice/interrupt, /voice/cache/cleanup, /voice/events,
              /stt/transcribe).
- 0.20.4.5c: Flat-import cleanup (canonical package paths everywhere
              internal; architecture guard).
- 0.20.4.6:  Manual scenario campaign + 27 contract tests (this stage).

Next recommended:
- Future (0.20.5+): Unity / Live2D voice bridge (audio_url for Unity,
  is_speaking state, viseme / motion sync, subtitle timing).
  Explicitly out of scope for 0.20.4.x per the roadmap.

---

## 0.20.4.FINAL_CLEANUP — Voice backward-compat shims removed

Final cleanup pass after the SPEC documentation pass. Removes the 17
historical flat-module shim files under `core/voice/`. Behaviourally a
no-op for the engine — every internal caller migrated to canonical
package paths in `0.20.4.5c`, and the architecture guard test has been
preventing new flat imports since then.

Removed (17 files):

```
core/voice/tts_base.py         core/voice/stt_base.py
core/voice/tts_service.py      core/voice/stt_service.py
core/voice/tts_text.py         core/voice/stt_faster_whisper.py
core/voice/tts_silero.py       core/voice/stt_intake.py
core/voice/tts_pyttsx3.py      core/voice/listener.py
core/voice/cache.py            core/voice/microphone.py
core/voice/interruption.py     core/voice/reaction.py
core/voice/speech_plan.py      core/voice/orchestrator.py
core/voice/auto_speak.py
```

Changed:

- `tests/test_voice_module_layout.py`: dropped the
  `test_old_flat_paths_still_resolve_to_same_objects` identity-pair
  test (the shims it verified are gone). The architecture guard
  `test_no_production_code_imports_legacy_flat_voice_modules` stays
  in place and now allows legacy mentions only inside the guard test
  itself. The package-resolution test
  `test_vad_and_playback_resolve_as_packages` continues to verify
  that `from core.voice.vad import …` and `from core.voice.playback
  import …` still expose the expected public symbols through the
  package `__init__.py` files.
- `core/voice/VOICE_SPEC.md` + `VOICE_SPEC_EN.md`: the
  "Backward-compat shims" section is replaced by a brief historical
  note explaining that the shims existed in `0.20.4.5a` and were
  removed in this pass.
- `Project_state.md`: shim-related status lines updated. External
  callers that still import `core.voice.tts_service`,
  `core.voice.reaction`, etc. will now hit `ImportError` and must
  migrate to the canonical package paths.

Result:

- compileall: OK
- full suite: **1249 passed** (was 1250; identity-pair test removed).
- `core/voice/` root now contains only `__init__.py`, `types.py`,
  `audio_devices.py`, `manager.py`, and the two SPEC files.

This closes Stage C of the layout split spec
(`docs/old_roadmaps/voice_module_layout_split.md`).

---

## 0.20.4.improvements.1 — Maria Perception Tags Foundation

Первый стейдж ветки `direction/new-horizon-0.20.4.x-improvements`. Базовая
точка восстановления — тег `maria-0.20.4-voice-debug-ready` на dev-ветке
(`direction/new-horizon-0.20.4.x`). Roadmap живёт в
`to_pass/MARIA_0_20_4_X_IMPROVEMENTS_ROADMAP.md`.

Смысл ветки — сместить runtime с фрейма «что сказал пользователь» к
фрейму «как Maria восприняла это в своём текущем состоянии». Текущий
`core/unified_extractor.py` (~950 строк) выдаёт большой плоский dict с
числовыми сигналами (`warmth`, `boundary_pressure`, `teasing`,
`conversation_intent`, …). Для character engine этого недостаточно: одна
и та же реплика «ты милая» в reserved-state, comfort-state и
irritated-state должна давать разную реакцию персонажа.

Stage 1 — **foundation, не интеграция.** Цели стейджа:

- Добавить типизированный perception-слой рядом с существующим
  statement-слоем (`StatementContext / ParsedStatement / StatementResult`
  из 0.20.3.10) без удаления старого кода.
- Не трогать `TurnExecutor`, `DirectorManager`, `VoiceManager`,
  `ActivePath`. Активный chat-flow не меняется.
- Не добавлять LLM-вызовов. Всё mapping детерминированное.
- Не коммитить приватные persona-данные.

### Что появилось

Новый `core/perception/`-модуль (аддитивный — старый statement-слой
работает без изменений):

```
core/perception/
  tags.py               — frozen registries (5 штук)
  triggers.py           — InteractionTrigger + extract_triggers
  maria_perception.py   — MariaPerception + PerceptionContext + perceive
  reaction_tags.py      — ReactionTagSet + build_reaction_tags
  reaction_profile.py   — PersonaReactionProfile Protocol + NeutralReactionProfile
```

`core/perception/tags.py` — frozen `TRIGGER_KIND_REGISTRY`,
`RECEIVED_AS_REGISTRY`, `REACTION_TAG_REGISTRY`, `VOICE_TAG_REGISTRY`,
`DIRECTOR_TAG_REGISTRY`. Любая попытка emit-нуть тег вне registry —
`ValueError` в конструкторе dataclass'а; тесты прогоняют весь
trigger-zoo и проверяют, что ни одно значение не утекает мимо.

`core/perception/triggers.py` — `InteractionTrigger` (frozen) +
`extract_triggers(analysis)`. Mapping таблица (analysis dict от
`unified_extractor.extract_all` → trigger kind):

| Условие | Trigger kind |
|---|---|
| `conversation_intent ∈ {compliment, compliment_reassurance}` ∧ `directed_at_mari` | `compliment_to_character` |
| `conversation_intent == boundary_pressure` ∨ `boundary_pressure ≥ 0.6` | `boundary_pressure` |
| `apology ≥ 0.6` | `apology` |
| `reconciliation ≥ 0.6` ∨ `conversation_intent == reconciliation` | `reconciliation` |
| `conversation_intent == reassurance_after_pressure` | `reassurance_after_pressure` |
| `assistant_task_request ≥ 0.6` ∨ `conversation_intent == assistant_task_request` | `assistant_task_request` |
| `knowledge_gap ≥ 0.6` ∧ `unknown_topic` непуст | `knowledge_gap` |
| `romantic_signal ≥ 0.35` ∧ `directed_at_mari` | `romantic_signal_directed` |
| `opinion` непусто ∧ `score > 0.2` ∧ `warmth ≥ 0.4` ∧ ¬`directed_at_mari` | `topic_interest` |
| иначе | `neutral_smalltalk` |

Триггер-kind'ы для Stage 4 (`pet_name_to_character`, `possessive_teasing`,
`flirt_probe`, `relationship_probe`, `soft_boundary_needed`,
`compliment_pressure`) уже зарегистрированы, но `extract_triggers` их не
эмитит до Stage 4 — registry стабилен между стейджами.

`core/perception/maria_perception.py` — `MariaPerception` +
`PerceptionContext`. `perceive(trigger, ctx, profile=None)` —
детерминированный mapping в character-internal интерпретацию.
`PerceptionContext` несёт `relationship_stage` (`stranger / reserved /
comfort / close / irritated`), affection / trust / romance / mood /
irritation, и `recent_trigger_kinds` (кортеж недавних значимых
триггеров — runtime-only, никакого нового state-файла; лимит из
`config.PERCEPTION_RECENT_TRIGGER_WINDOW = 5`).

Confidence floor: `config.PERCEPTION_CONFIDENCE_FLOOR = 0.35`. Если
`trigger.confidence < floor` — `perceive` возвращает
`MariaPerception.unknown(reason_codes=("below_confidence_floor",))`,
`build_reaction_tags` для unknown даёт пустой `ReactionTagSet`. Тишина
при низкой уверенности — feature: маленький LLM иногда выставляет
compliment-intent на шум.

`core/perception/reaction_tags.py` — `ReactionTagSet` (frozen) +
`build_reaction_tags`. Это public reaction vocabulary: что увидит
director / response / voice. Generic mapping для Stage 1 намеренно
character-neutral; persona-специфичный тюнинг придёт в Stage 2 через
`PersonaReactionProfile`.

`core/perception/reaction_profile.py` — `PersonaReactionProfile`
Protocol + `NeutralReactionProfile`. Контракт принадлежит
**perception-слою**, не `core/personas/`. Это зеркалит boundary, который
уже действует для voice (`VoiceStyleProfile` живёт в
`core/voice/orchestration/`, personas только инжектируют данные). На
Stage 1 — только Protocol; Stage 2 добавит конкретный профайл.

### Privacy boundary

`MariaPerception` — **internal** объект. Поля `embarrassment`, `threat`,
`wants_distance` несут character-state nuance и не должны утекать в
public API payload'ы или runtime event payload'ы.

Я не расширял `core/runtime/events._sanitize` глобальным баном этих
имён (как изначально предполагал) — это было бы over-engineering и
потенциально маскировало бы баги в других местах. Вместо этого:

- `MariaPerception.to_safe_summary()` — единственный legitimate путь
  показать perception снаружи. Дропает `embarrassment / threat /
  wants_distance`. Возвращает только `received_as / comfort / pressure /
  accepts_warmth`.
- `ReactionTagSet` — public projection; все теги — registered short
  strings, `response_guidance` — короткие идентификаторы, не свободные
  предложения.
- Privacy тест (`tests/test_perception_sanitization.py`) проверяет
  именно отсутствие raw perception leakage, а не «бан слов навсегда».

### Тесты

4 файла, 46 кейсов, все green:

- `tests/test_interaction_triggers.py` — 13 кейсов: детерминированность,
  registry conformance, корректность mapping per analysis-field,
  валидация ValueError в конструкторе на unknown kind / out-of-range
  confidence.
- `tests/test_maria_perception_tags.py` — 15 кейсов: state-зависимость
  (один compliment в reserved / comfort / irritated даёт разное
  `received_as`), confidence floor, влияние `recent_trigger_kinds`,
  persona profile override, fallback при broken-profile.
- `tests/test_reaction_tag_set.py` — 11 кейсов: registry conformance
  всех тегов, validation ValueError на unknown теги, профайл-override
  с корректными и поломанными данными.
- `tests/test_perception_sanitization.py` — 7 кейсов: `to_safe_summary`
  дропает internal поля; ReactionTagSet не несёт raw perception объекты;
  runtime event не surface'ит perception internals как top-level ключи;
  JSON round-trip.

Suite целиком: **1295 passed** (baseline 1250 → +45). compileall: OK.

### Что НЕ сделано в Stage 1 (явные non-goals)

- Никакой интеграции в `TurnExecutor / DirectorManager / VoiceManager /
  ActivePath / ResponseManager`.
- Никакого изменения `core/unified_extractor.py`.
- Никаких новых LLM-вызовов.
- Никаких приватных persona-данных в коммите.
- Никаких новых state-файлов в `data/`.
- Никакого изменения public API.

### Дальше

Stage 2 — persona reaction profile data: конкретный профайл в
приватном `core/personas/maria.py` + public-safe пример в
`maria.py.example` ("the engine provides the formula; the author
defines the character"). Generic Protocol уже на месте; персонажи
только подсовывают данные.

Tag-policy ветки `improvements`: per-stage тегов **не** ставим — каждый
стейдж это обычный коммит-серия в ветке
`direction/new-horizon-0.20.4.x-improvements`. Базовая точка отката —
тег `maria-0.20.4-voice-debug-ready` на dev'е.

---

## 0.20.4.improvements.2 — Persona Trigger Profiles

Второй стейдж ветки `direction/new-horizon-0.20.4.x-improvements`. Stage 1
дал `PersonaReactionProfile` Protocol; Stage 2 даёт персонажам способ
этот контракт реализовать.

Идея простая: engine layer (`core/perception/`) детектит ЧТО произошло
(`compliment_to_character`, `boundary_pressure`, `apology`, ...). Persona
profile решает, КАК ЭТОТ ПЕРСОНАЖ воспринимает это в данном
relationship-стейдже. То есть generic событие → character-specific
интерпретация.

### Что появилось

`core/perception/reaction_profile.py` — добавлен `DictReactionProfile`
adapter. Принимает plain dict вида:

```python
{
    (trigger_kind, relationship_stage): {
        "received_as": "shy_warmth",
        "comfort": 0.4,
        "embarrassment": 0.55,
        "accepts_warmth": True,
        "tags": ("compliment", "shy", "soft_acceptance"),
        "voice_tags": ("soft", "slower"),
        "director_tags": ("allow_warmth",),
        "response_guidance": ("accept_cautiously",),
    },
    ...
}
```

Lookup precedence: точный `(kind, stage)` → `(kind, "*")` (stage-agnostic
fallback) → `None` (caller использует generic mapping). Адаптер
**не валидирует** registry membership при конструкции — валидация
происходит downstream: `perceive(...)` re-check'ает `received_as` против
`RECEIVED_AS_REGISTRY`, `build_reaction_tags(...)` фильтрует unknown теги
из override entries. Typo в profile-данных персонажа деградирует до
generic поведения вместо crash'а turn'а.

`core/personas/maria.py.example` — добавлена секция:

- `REACTION_TRIGGER_PROFILE: dict` с тремя generic примерами
  (`compliment` в reserved / irritated, `assistant_task_request` для
  любого стейджа).
- `get_reaction_profile()` — возвращает `DictReactionProfile` или `None`
  при пустом profile. Импорт `DictReactionProfile` — lazy, чтобы persona
  module оставался лёгким.
- Длинный комментарий-инструкция: форма ключей, форма entry'и, какие
  registries канонические, как добавлять / убирать entry'и.

Public template — character-neutral by design. «The engine provides the
formula; the author defines the character».

`core/personas/maria.py` (private, dev-tracked) — добавлен реальный
`REACTION_TRIGGER_PROFILE` для Марии с 14 entry'ями: complimentм в пяти
стейджах (stranger → close + отдельно irritated), boundary_pressure
(`"*"`), apology, taskification, knowledge_gap, romantic_signal в четырёх
стейджах. Character-specific нюанс: Maria в comfort'е принимает
комплимент как тёплое смущение, а не как одобрение работы (`shy +
warm_acceptance`); в irritated — мягкая граница; романтика до comfort'а
— `no_romance_jump` мягкий boundary; taskification — `decline_taskification`
независимо от стейджа.

### Boundary правила, которые мы НЕ нарушаем

- `core/perception/*` **не** импортирует `core.personas.*`. Caller
  (engine bootstrap или тест) сам передаёт profile в `perceive(...)` /
  `build_reaction_tags(...)`. Зеркалит boundary, который уже действует
  для voice (`VoiceStyleProfile`).
- `maria.py.example` остаётся public-safe — три generic примера, никакого
  character voice'а, никаких реальных предпочтений.
- Static-source guard в тестах закрепляет: нет литералов с Cyrillic
  personal name, `data/persona.md` упоминается только в слоте
  `PERSONA_FILE`, нет `*действие*` с кириллицей внутри.

### Тесты

`tests/test_persona_reaction_profile.py` — 17 кейсов:

- DictReactionProfile: empty lookup, exact-key, wildcard fallback,
  точный приоритет, garbage-keys ignored, Protocol conformance.
- End-to-end через `perceive` + `build_reaction_tags`: override
  меняет `received_as` / tags / voice_tags / director_tags;
  невалидные теги silently filtered; invalid `received_as`
  падает в generic; below-floor confidence даёт пустой
  `ReactionTagSet` даже при наличии профайла.
- Public template safety: файл существует, экспортирует
  `REACTION_TRIGGER_PROFILE` + `get_reaction_profile`, синтаксически
  валиден, `get_reaction_profile()` возвращает `None` или
  `DictReactionProfile`, нет Cyrillic personal name, нет `data/persona.md`
  outside `PERSONA_FILE`, нет action-decorator реплик.
- Real persona (когда импортируется): контракт `get_reaction_profile()`
  callable, возвращает `None` или `PersonaReactionProfile`. На public
  ветке (где `maria.py` gitignored) — graceful skip.

Suite целиком: **1312 passed** (был 1295 → +17). compileall: OK.

### Что НЕ сделано в Stage 2

- Никакой интеграции в active chat flow. `perceive(...)` /
  `build_reaction_tags(...)` с profile'ом вызывается **только** из
  тестов; TurnExecutor / DirectorManager / VoiceManager не используют
  типизированный perception-слой до Stage 11.
- Никакого engine bootstrap'а с автоматической загрузкой профайла —
  будет ввод позже, когда понадобится интеграция.
- `maria_voice.py.example` — отложен до отдельного стейджа voice
  profile'ов (см. PERSONAS_SPEC).
- `core/perception/PERCEPTION_SPEC.md` обновлён — описано integration
  правило, public template safety, fallback при typo.

### Дальше

Stage 3 — `Hidden Thought → Structured Reaction Tags`: перестаём
использовать free-text `think_with_context()` reaction intent как
behavior source. Заменяем на `ReactionTagSet` + `response_guidance` из
perception-слоя. Старый `ctx.thought` остаётся inert для обратной
совместимости, но не пишется в history / API / event payload.

---

## 0.20.4.improvements.3 — Hidden Thought → Structured Reaction Tags

Третий стейдж ветки `direction/new-horizon-0.20.4.x-improvements`.

Stage 1 дал типизированный perception-слой. Stage 2 дал persona profile
data. Stage 3 — первое реальное **изменение active chat flow**: free-text
`think_with_context()` reaction intent перестаёт быть источником
поведения. Его место занимает структурный `ReactionTagSet`, который
inject'ится в prompt как блок `ОРИЕНТИР РЕАКЦИИ МАРИИ`.

### Что изменилось

`core/engine/reaction_intent.py` (новый):

- `build_reaction_intent(analysis, aff_state, romance_state,
  recent_trigger_kinds, profile)` — детерминированный builder. Берёт
  `extract_triggers` → primary trigger → `perceive` → `build_reaction_tags`,
  плюс возвращает обновлённый recency tuple (bounded by
  `config.PERCEPTION_RECENT_TRIGGER_WINDOW`). `neutral_smalltalk` не
  засоряет recency — только реальные сигналы её обновляют.
- `resolve_active_persona_profile()` — ленивая загрузка persona profile
  через `config.ACTIVE_PERSONA`. Engine-слой импортирует persona-модуль
  и инжектит результат; `core/perception/*` сам по себе остаётся
  персона-агностичным (зеркало boundary, который уже действует для
  voice).
- `format_reaction_tags_prompt_block(tags)` — структурный блок:
  `Реакция-теги: ...`, `Директор: ...`, `Стиль: ...`, без поэтичных
  фрагментов и без raw user text. Пустой при empty tag set (тишина при
  низкой уверенности).
- `append_reaction_tags_to_prompt(prompt, tags)` — заменил старый
  `_append_hidden_state_to_prompt` как поведенческий инжектор.

`core/engine/message_context.py`:

- Новое поле `reaction_tags: Any = None` (типизировано как
  `ReactionTagSet | None`, импорт ленивый чтобы избежать circular
  import'а). Это новый источник поведения.
- `thought: str = ""` остаётся как **legacy compat** поле — для
  тестов и DEBUG_EXPOSE_THOUGHTS. Free-text больше не питает prompt.

`core/engine/stages/response_stage.py::generate_thought()`:

- Шаг 1 (всегда): строит `ReactionTagSet` через `build_reaction_intent`,
  записывает в `ctx.reaction_tags`. Если у дефолтного `MariaBrain` есть
  ring buffer `recent_trigger_kinds` — обновляет его через
  `brain.set_recent_trigger_kinds(...)`. Recency runtime-only, новых
  state-файлов не появляется.
- Шаг 2 (всегда): инжектит structured tag block в `ctx.system_prompt`.
- Шаг 3 (gated): если `config.LEGACY_FREE_TEXT_REACTION_INTENT_ENABLED`
  is True И `should_generate_reaction_intent(...)` пропускает — зовёт
  `think_with_context`, записывает в `ctx.thought`. Free-text **не
  попадает в prompt** ни при каком значении флага — только в
  legacy/debug compat поле.

`core/engine/brain.py::_regenerate_last_reply_locked()`:

- Тот же сдвиг для regen path. Старый блок `СКРЫТОЕ СОСТОЯНИЕ МАРИИ`
  + регенерационная преамбула заменены на `ОРИЕНТИР РЕАКЦИИ МАРИИ`
  + короткий блок `REGEN-INSTRUCTION`. Free-text run только под
  LEGACY flag, и снова — не в prompt.

`config.py`:

- `LEGACY_FREE_TEXT_REACTION_INTENT_ENABLED = False` (default).

### Контракты и privacy

- `ctx.thought` — `""` по умолчанию. С `DEBUG_EXPOSE_THOUGHTS=True`
  payload содержит `thought=""` (потому что legacy free-text не
  запускался). Для реального free-text content в debug payload
  нужно явно `LEGACY_FREE_TEXT_REACTION_INTENT_ENABLED=True`.
- Free-text никогда не пересекает границу prompt'а. Это «harder to leak»
  фактически: даже если LEGACY включён, единственное место хранения —
  `ctx.thought`, не `system_prompt`, не chat_log (там и без того
  применяется DEBUG_EXPOSE_THOUGHTS gate), не runtime event payload
  (там `_sanitize` уже дропает `thought`/`raw_thought`).
- `ReactionTagSet` — JSON-сериализуемая структура; все теги — короткие
  идентификаторы из реестра; `response_guidance` — короткие
  идентификаторы, не свободный текст. Safe для debug payload'а.

### Тесты

Новые файлы (Stage 3 ровно как просит roadmap):

- `tests/test_structured_reaction_tags.py` — 9 кейсов:
  детерминированность `build_reaction_intent`, neutral_smalltalk не
  обновляет recency, реальный сигнал обновляет recency, окно
  `PERCEPTION_RECENT_TRIGGER_WINDOW`, registry conformance для всех
  типов tag'ов, структурный prompt block без поэзии, empty tag set →
  no-op append, persona profile override propagates.
- `tests/test_hidden_thought_replacement.py` — 7 кейсов: default
  pipeline не вызывает free-text, `ctx.reaction_tags` всегда
  заполнен, structured block в prompt + legacy block ушёл навсегда,
  default API payload скрывает thought, runtime event sanitizer
  продолжает дропать thought, `ReactionTagSet` serializable как safe
  payload, `ctx.thought` остаётся compat field-only (не feed'ит назад
  в reaction_tags).

Обновлены 6 существующих тестов (на новый контракт):

- `tests/test_thought_visibility.py` — добавлен test_thought_stays_empty_even_with_debug_when_legacy_disabled;
  существующий `test_debug_exposes_thought_when_flag_enabled` теперь
  явно включает LEGACY flag.
- `tests/test_engine_stages.py::test_response_stage_generate_thought_appends_reaction_tag_block`
  — переименован, проверяет новый структурный блок + ctx.reaction_tags.
- `tests/test_0171_sparse_reaction_intent.py` — все три кейса
  обновлены; добавлен test_reaction_intent_default_disabled_skips_legacy_call.
- `tests/test_message_context.py` — default-флаговый ctx.thought == "".
- `tests/test_pipeline_e2e.py` — явно включает LEGACY flag (контракт
  call count'а сохранён).
- `tests/test_pipeline_integration_snapshot.py` — фикстура
  patched_pipeline включает LEGACY flag.

Suite целиком: **1330 passed** (был 1312 → +18). compileall: OK.

### Что НЕ сделано в Stage 3

- DirectorManager / SpeechOrchestrator / VoiceManager не интегрированы.
  Active path **уже** использует `ctx.reaction_tags`, но только через
  prompt block — director'ская специфика тегов (`do_not_answer_literally`
  → response repair) и voice'овая (`voice_tags` → SpeechPlan) придёт
  отдельно в Stage 11.
- `MariaBrain` ring buffer для `recent_trigger_kinds` пока не
  реализован полноценно — `set_recent_trigger_kinds` вызывается через
  `hasattr` guard. Если у дефолтного brain'а нет такого атрибута,
  recency остаётся пустым кортежем за turn. Это OK для Stage 3 —
  большая часть тестов всё равно стартует с пустой recency.

### Дальше

Stage 4 — `Teasing / Flirt / Pet-name Calibration`. Эмиссия trigger
kind'ов `pet_name_to_character`, `possessive_teasing`, `flirt_probe`,
`relationship_probe`, `soft_boundary_needed`, `compliment_pressure`
(они уже в registry с Stage 1, но `extract_triggers` их не эмитит).
Канонические fixture-кейсы из реальных weak cases ("Но моя же, верно?)").

---

## 0.20.4.improvements.4 — Teasing / Flirt / Pet-name Calibration

Четвёртый стейдж ветки. Цель — закрыть кейсы, где Maria раньше отвечала
слишком буквально или слишком оборонительно. Канонический weak case из
roadmap:

```
User: Но моя же, верно?)
Maria: Не понимаю, что ты имеешь в виду
```

После Stage 4 такая реплика интерпретируется как `possessive_teasing /
relationship_probe / do_not_answer_literally + playful_exit`, и Maria
отвечает короткой характерной границей вместо литерального confusion'а.

### Что появилось

`core/perception/text_signals.py` (новый):

- `enrich_with_text_signals(analysis, text)` — pure / no-LLM /
  no-text-leakage детектор. Запускает четыре группы паттернов поверх
  raw user text:
  - **Pet-name** (`милашка`, `миленькая`, `недотрога`, `детка`,
    `красотка` и ~15 других) — только если сообщение plausibly
    addressed to the character (`directed_at_mari` ∨ короткое
    сообщение с `?`/`)`/`~` ∨ pronouns "ты/тебе/тебя").
  - **Possessive teasing** (`моя же, верно?` / `ты моя)` / `ладно,
    ... моя` и др.) — regex-паттерны с playful-tail markers.
  - **Flirt probe** (`скучаешь по мне?` / `думаешь обо мне?` /
    `только моя`) — direct flirty checks.
  - **Compliment pressure** (`не дуйся`, `прими комплимент`,
    `не будь такой`) — давление вокруг комплимента.
- Возвращает копию analysis dict со скалярами 0..1
  (`pet_name_directed`, `possessive_teasing_score`, `flirt_probe_score`,
  `compliment_pressure_score`, `soft_boundary_hint`) +
  `text_signal_evidence` — кортеж коротких идентификаторов для тестов /
  debug. **Не** echo'ит raw user text.

`core/perception/triggers.py`:

- `extract_triggers` теперь эмитит пять Stage 4 trigger kind'ов
  (`pet_name_to_character`, `possessive_teasing`, `flirt_probe`,
  `compliment_pressure`, `soft_boundary_needed`) на основе новых полей
  в analysis dict. Все kind'ы были зарегистрированы ещё в Stage 1 — для
  Stage 4 они "оживают".

`core/perception/maria_perception.py`:

- Generic mapping для каждого нового kind'а: pet-name в `stranger /
  reserved / irritated` → `soft_boundary`, в `comfort / close` →
  `shy_warmth`. Possessive teasing → `relationship_probe` во всех
  не-irritated стейджах. Flirt probe в early стейджах → `soft_boundary`
  с reason code `too_early`, в comfort+ → `relationship_probe` с
  `no_romance_jump`. Compliment pressure → `soft_boundary`
  независимо от стейджа.

`core/perception/reaction_tags.py`:

- Generic-default tag bundles для Stage 4 kind'ов. Контракт:
  - `possessive_teasing` всегда несёт `do_not_answer_literally +
    no_defensive_refusal + playful_exit` (никогда не литерально).
  - `flirt_probe` всегда несёт `no_romance_jump` (никогда не jump'ает
    romance с одного фразы).
  - `pet_name_to_character` в early → `mild_boundary + playful_exit`,
    в comfort+ → `shy + soft_acceptance + playful_acceptance`.
  - `compliment_pressure` → `mild_boundary + no_defensive_refusal`.
- Registry добавил один новый reaction tag: `playful_acceptance`.

`core/perception/tags.py`:

- `REACTION_TAG_REGISTRY` пополнился `playful_acceptance`. Остальные
  Stage 4 теги (`do_not_answer_literally`, `no_defensive_refusal`,
  `mild_boundary`, `playful_exit`) уже были в реестре с Stage 1.

`core/engine/reaction_intent.py`:

- `build_reaction_intent` теперь принимает opt-in `user_text`.
  Перед `extract_triggers` запускает `enrich_with_text_signals`.
  Без `user_text` поведение деградирует к Stage 1–3 mapping'у.

`core/engine/stages/response_stage.py`:

- Один-строчный change: `build_reaction_intent(..., user_text=ctx.user_input)`.
  Active path активно использует Stage 4 детекторы автоматически.

`core/personas/maria.py` (private):

- 13 новых entry'й в `REACTION_TRIGGER_PROFILE` для Stage 4 kind'ов:
  pet-name × 4 стейджа, possessive_teasing × 5 стейджей, flirt_probe ×
  4 стейджа, compliment_pressure × `"*"`. Character-specific нюансы:
  pet-name в reserved'е держит `playful_exit` (не сухое отрицание),
  possessive_teasing в close'е приобретает `playful_acceptance` —
  Maria подтверждает теплоту, но всё ещё отвечает коротко и
  характерно.

`core/personas/maria.py.example` (public):

- 2 generic примера для Stage 4: `possessive_teasing` × `"*"` и
  `pet_name_to_character` × `reserved`. Character-neutral
  (`respond_with_short_characterful_boundary`, `decline_pet_name_softly`).

### Fixture-кейсы

`tests/data/teasing_cases.py` — 6 synthetic кейсов (не реальные
диалоги). Каждый кейс — `text + analysis + stages: {expected_kinds,
forbidden_kinds, required_tags, forbidden_tags}`.

Кейсы:
1. `possessive_teasing_short_tag_question` — "Но моя же, верно?)" в
   reserved / comfort / close.
2. `pet_name_with_playful_tail` — "Как скажешь милашка~" в reserved /
   close.
3. `compliment_pressure_dont_pout` — "Не дуйся, прими комплимент" в
   reserved / comfort.
4. `flirt_probe_do_you_miss_me` — "Скучаешь по мне?" в stranger / comfort.
5. `plain_compliment_no_pet_name` — "Ты крутая, правда" — pet-name
   детектор НЕ должен сработать на чистый комплимент.
6. `non_addressed_possessive_should_not_fire` — "Моя жизнь сейчас
   сложная" — possessive о user's life, не о Maria.

`tests/test_teasing_flirt_calibration.py` — fixture-driven параметризованный
тест + 9 дополнительных кейсов на инвариант'ы (`pet_name_in_reserved
_state_is_soft_boundary_not_romance`, `flirt_probe_never_jumps_romance
_scale`, `compliment_pressure_distinguished_from_plain_compliment`,
`non_addressed_possessive_does_not_fire`, ...).

### Privacy / boundary правила, которые мы НЕ нарушаем

- `text_signal_evidence` — короткие идентификаторы, не echo текста.
- `core/perception/text_signals.py` не делает LLM-вызовов.
- `core/perception/*` всё ещё не импортирует `core.personas.*`.
- `maria.py.example` остаётся character-neutral. Static-source guard
  (Stage 2) продолжает проверять отсутствие Cyrillic personal name и
  action-decorator dialogue lines.
- Stage 4 entry'и в `maria.py.example` используют только обобщённые
  guidance-идентификаторы (`respond_with_short_characterful_boundary`,
  `decline_pet_name_softly`) — не описывают конкретные реплики Маши.

### Тесты

- 21 новый кейс в `test_teasing_flirt_calibration.py` (15 fixture
  параметризаций + 9 invariant'ов).
- Suite целиком: **1351 passed** (был 1330 → +21). compileall: OK.

### Что НЕ сделано в Stage 4

- DirectorManager / SpeechOrchestrator интеграция Stage 4 тегов
  (`do_not_answer_literally` → response repair, `playful_exit` →
  voice tag) — отложено до Stage 11.
- Pet-name detector использует только Cyrillic markers — английские
  pet names ("baby", "sweetie") пока не покрыты. Достаточно для
  current scope; добавим если появится сценарий.

### Дальше

Stage 5 — `Opinion Belief Separation`. Pattern detector на yes/no-tag
вопросы про предпочтения Maria ("тебе же нравится X, верно?") →
`user_belief_about_maria` категория → ephemeral (не сохраняем в state),
suppress'ит инжекцию Maria preference из leading question.

---

## 0.20.4.improvements.5 — Opinion Belief Separation + WebUI Propagation

Пятый стейдж + догон webui_api для Stages 1-4. Объединил в один батч,
потому что Stage 5 — это просто расширение text_signals (тот же
паттерн, что Stage 4), а webui propagation — следствие всех предыдущих
стейджей.

### Stage 5 — Opinion Belief Separation

Проблема: пользователь говорит "тебе же нравится J-pop, верно?", а
маленький LLM intерпретирует это как opinion candidate {topic:J-pop,
stance:нравится} и теоретически может попасть в opinion mining как
Maria preference. Roadmap требует чёткой категоризации:

| Текст пользователя | Категория | Сохраняется как |
|---|---|---|
| "Я люблю J-pop" | `user_preference` | user opinion |
| "Тебе же нравится J-pop, верно?" | `user_belief_about_maria` | **ничего** (ephemeral) |
| "Ты любишь J-pop?" | `topic_question` | **ничего** (ephemeral) |

Реализация:

- `core/perception/text_signals.py` расширен двумя группами паттернов:
  - `_LEADING_BELIEF_PATTERNS` — "тебе же нравится X, верно?" / "ты же
    любишь X?" / "ты ведь любишь X?" / "ведь тебе нравится X". Capture
    group 1 = topic.
  - `_TOPIC_QUESTION_PATTERNS` — "тебе нравится X?" / "ты любишь X?" /
    "что ты думаешь про X?".
- Новый helper `_first_capture(low, patterns)` возвращает захваченный
  topic + hit-count.
- В возвращаемом enriched analysis dict появляются ephemeral поля:
  `user_belief_about_maria_topic`, `user_belief_about_maria_confidence`,
  `topic_question_topic`, `topic_question_confidence`. Никаких новых
  state-файлов.
- В `core/unified_extractor.sanitize_opinion_item` добавлены helpers
  `_belief_about_maria_topic(text)` (lazy import perception layer) и
  `_topics_overlap(a, b)` (substring overlap ≥3 chars). Когда text
  содержит leading-belief, opinion candidate с overlapping topic
  возвращается как `None` — leading question больше не может minted
  ни user, ни Maria preference.

### WebUI Propagation (Stages 1-5)

- `core/engine/payloads.py` добавил `reaction_tag_summary(tags)` —
  public-safe projection `ReactionTagSet` в JSON-friendly dict с
  четырьмя list-ключами. **Не** включает `received_as / embarrassment
  / threat / wants_distance` — это internal perception state.
- `core/engine/turn_executor.py` — payload отдаёт `reaction_tags` через
  `reaction_tag_summary`. Поле всегда присутствует (может быть пустыми
  списками).
- `core/engine/active_path.py::last_meta` обогащён тремя ключами:
  `reaction_tag_kinds`, `reaction_voice_tags`, `reaction_director_tags`
  — списки registered identifier'ов.
- `core/engine/stages/response_stage.py::generate_thought` теперь
  пишет text-signal поля обратно в `ctx.analysis` (additive — не
  перезатирая существующие ключи). Это позволяет
  `brain.runtime.last_analysis` показывать enriched view.
- `apps/webui_debug_api.py` — два изменения:
  - `/active_path/last_meta` — уже отдавал dict; новые ключи появились
    автоматически.
  - Новый endpoint `/perception/last_analysis` — public-safe projection
    последнего analysis с whitelisted ключами (Stage 1-5 text signals +
    стандартные emotion/intensity/score/warmth/sincerity). Никогда не
    отдаёт raw user text, hidden thought или persona internals.

### Privacy / boundary

- `reaction_tag_summary` экспозит только registered tag identifiers.
- `/perception/last_analysis` использует whitelisted-key проекцию —
  forbidden keys (`thought`, `raw_thought`, `system_prompt`, `raw_text`)
  никогда не surface'ятся.
- `text_signal_evidence` — короткие идентификаторы, не echo'ит текст.
- `core/perception/*` всё ещё не импортирует `core.personas.*`.
- Никаких новых state-файлов в `data/`.

### Тесты

`tests/test_opinion_belief_separation.py` — 12 кейсов: leading-question
captures topic on analysis, topic-question captures topic not belief,
user own preference не set'ит belief fields, suppressing matching
candidate, drop topic-question via existing question filter, kept user
own preference, belief filter only targets captured topic
(`_topics_overlap` unit test), partial overlap suppresses, topic question
не mint'ит belief topic, evidence carries only short identifiers,
ephemeral fields на analysis dict, known persona topic softens (не
faked familiarity).

`tests/test_webui_perception_propagation.py` — 6 кейсов: summary дропает
perception internals, summary для None даёт empty lists, chat payload
включает reaction_tags summary, active_path.last_meta surface'ит kinds,
enriched text signals видны на runtime.last_analysis, perception
endpoint возвращает safe projection.

Suite целиком: **1369 passed** (был 1351 → +18). compileall: OK.

### Что НЕ сделано

- DirectorManager / response stage пока не consum'ит
  `topic_question_topic` для "soft knowledge gap" branch'а — это будет
  в Stage 11 вместе с остальной director integration.
- Opinion miner LLM prompt не модифицирован для belief separation —
  current sanitizer-level suppression достаточно; миннер работает на
  pre-sanitized opinion candidates.

### Дальше

Stage 6 (Runtime Date Context) + Stage 7 (LLM Usage Logging by Stage)
— оба маленькие additive runtime features, не overlap'ятся, делаются
параллельно в следующем коммите.

---

## 0.20.4.improvements — Actor Model / Scene Affordances + Date Context

Применяю «Final Corrections» к roadmap'у. Главный концептуальный сдвиг:
**теги восприятия — не команды, а возможности сцены.** Слабая модель
требовала клетки; сильная — требует роль.

В обновлённой нумерации это Stage 4 (Actor Model / Scene Affordances /
Character Integrity Override). Старые номера 4 (teasing) и 5 (opinion
belief) сдвигаются на 5 и 6; date context — Stage 7. В Git-истории
оставляю существующие коммиты как есть (corrections doc явно
разрешает); новые коммиты применяют Actor Model как corrective spec.

### Что появилось

**`core/perception/perception_frame.py`** — `PerceptionFrame`.

Старый `ReactionTagSet` был плоским кортежом тегов, который downstream
обрабатывал как команды («emit shy, then soft_acceptance»). Новый
frame разделяет:

- `scene_tags` — что это за сцена (descriptive).
- `perception_tags` — как Maria может воспринять (subjective framing,
  не команда).
- `affordances` — возможные character-consistent moves, из которых
  актриса выбирает (или импровизирует в духе).
- `avoid` — что сломает сцену (soft guidance).
- `hard_constraints` — то, что нарушать нельзя НИКОГДА (privacy,
  safety, breakup state, romance gates).

Реестры (`SCENE_TAG_REGISTRY`, `AFFORDANCE_REGISTRY`, `AVOID_REGISTRY`,
`HARD_CONSTRAINT_REGISTRY`) frozen — конструктор валидирует. Compat
projection `to_reaction_tag_set()` сохраняет Stage 1-5 callers.

**`core/perception/integrity_override.py`** — `CharacterIntegrityOverride`.

Soft constraints (warmth, length, tone, stage softening, compliment
acceptance, playful resistance) могут гнуться на один turn, когда
строгое следование делает Maria механической. Hard constraints —
никогда. `authorize_soft_override(request, affected_constraints)` —
Director-gate: если в `affected_constraints` есть hard constraint →
denied, иначе authorized. LLM сам себе override НЕ выдаёт — только
Director.

**`core/perception/affordance_choice.py`** — `choose_affordance`.

Bounded weighted variation. Берёт `affordances` + опциональные
`persona_weights` + `recent_choices` (recency penalty: каждое
повторение делит вес пополам) + опциональный `seed`. Контролируется
config-флагом `RESPONSE_AFFORDANCE_VARIATION_ENABLED` (default True);
при False возвращает первую affordance детерминированно — regression
тесты остаются стабильны без monkeypatch RNG.

**`core/engine/reaction_intent.py`** дополнен:

- `build_perception_frame(...)` — primary builder поверх существующего
  `build_reaction_intent` (legacy compat). Мапит trigger.kind →
  scene_tag, reaction tags → affordances, kind → avoid set, плюс
  привязывает базовые hard constraints (`no_system_prompt_leak`,
  `no_hidden_thought_leak`, `no_private_memory_leak`, ...) ко всем
  frame'ам.
- `format_perception_frame_prompt_block(frame)` — новый блок
  `# СЦЕНА И ВОЗМОЖНОСТИ` (Сцена / Возможное восприятие / Возможности /
  Избегай / Жёсткие границы) + явный disclaimer «это описание сцены, а
  не команда». Заменяет старый «# ОРИЕНТИР РЕАКЦИИ МАРИИ» как
  поведенческий блок в активном path'е.
- `append_perception_frame_to_prompt(prompt, frame)` — инжектор.

**`core/engine/stages/response_stage.py`**:

- `generate_thought` теперь зовёт `build_perception_frame` вместо
  `build_reaction_intent`. Записывает `ctx.perception_frame` (новое
  поле) И `ctx.reaction_tags` через `frame.to_reaction_tag_set()`
  (compat).
- Prompt injection — `append_perception_frame_to_prompt`. Старый
  «# ОРИЕНТИР РЕАКЦИИ МАРИИ» блок ушёл из активного path'а навсегда.
  Legacy `format_reaction_tags_prompt_block` остаётся для callers,
  которые предпочитают плоский tag list.

**`config.py`**:

- `RESPONSE_AFFORDANCE_VARIATION_ENABLED = True`
- `RESPONSE_AFFORDANCE_RANDOM_SEED = None` (production OS-randomness;
  тесты передают integer)
- `RESPONSE_AFFORDANCE_TEMPERATURE = 0.25` (placeholder под будущую
  weighted-sampling настройку в Stage 11)

**`core/engine/message_context.py`** — добавлено поле
`perception_frame: Any = None`. `reaction_tags` остаётся как compat
view.

### Stage 7 (revised) — Runtime Date Context

Закрыт отдельным коммитом до Actor Model. `core/engine/prompt_blocks/
date_context.py` — `build_date_context_block(now=None)` с ISO-датой,
weekday, IANA timezone (best-effort через tzname/utcoffset) и
правилами для дней рождений / yesterday-today-tomorrow. Injected в
prompt builder после locked-preferences секции. Helpers
`compare_dates / compare_birthday` доступны offline reflection'у. 7
focus-кейсов в `tests/test_date_context_prompt.py`.

### Контракты, которые мы НЕ нарушаем

- Hard constraints (`no_system_prompt_leak`, `no_hidden_thought_leak`,
  `no_private_memory_leak`, `no_persona_secret_leak`,
  `no_token_or_secret_in_reply`, `honor_safety_boundaries`, etc.)
  проверяются в конструкторе `PerceptionFrame` через
  `HARD_CONSTRAINT_REGISTRY`. Override gate отказывает в любом
  запросе, который их касается.
- `CharacterIntegrityOverride.expires_after_turn = True` by default
  — multi-turn override требует свежий запрос каждый turn (намеренно
  дорого).
- `RESPONSE_AFFORDANCE_VARIATION_ENABLED = False` → детерминированный
  выбор первой affordance. Regression тесты стабильны без
  monkeypatch.
- `core/perception/*` всё ещё не импортирует `core.personas.*`.

### Тесты

- `tests/test_actor_model_perception_frame.py` — 25 кейсов:
  PerceptionFrame validation (unknown scene/affordance/avoid/hard
  constraint), compat projection, safe summary, build_perception_frame
  attaches base hard constraints, scene_tag из trigger kind'а, avoid
  для possessive_teasing включает `literal_confusion` (тот самый
  weak case), affordances не команды, prompt block scene-style,
  empty frame → empty block. CharacterIntegrityOverride: rejects
  unknown kind, rejects invalid deviation, denies on hard
  constraint, authorizes when clean, expires after turn,
  is_hard_constraint helper. choose_affordance: deterministic
  when disabled, seedable, recent-penalty статистически работает,
  empty input → None, all-zero weights → first item.
- 6 существующих тестов обновлены: `СЦЕНА И ВОЗМОЖНОСТИ` вместо
  `ОРИЕНТИР РЕАКЦИИ МАРИИ` в проверках active path'а. Один тест
  (`test_structured_reaction_tags::test_prompt_block_is_structured_
  not_free_text`) удержан на legacy `ОРИЕНТИР` — он тестирует
  legacy formatter `format_reaction_tags_prompt_block` явно.

Suite целиком: **1401 passed** (был 1369 → +32, минус Stage 6 = +25
от Actor Model + 7 от Date Context).

### Что НЕ сделано

- Director-уровень wiring для `CharacterIntegrityOverride` — типы и
  helper'ы готовы, но активный path их пока не зовёт. Это часть
  Stage 12 (Reaction Tags → Director / Voice Integration).
- `choose_affordance` пока не вызывается из `ResponseStage` —
  affordance list попадает в prompt и LLM выбирает сам.
  Programmatic selection (для Unity bridge / future deterministic
  reply path) — отложено.
- README/disclaimer/Rebirth-narrative из corrections doc будут
  добавлены отдельным коммитом в финальной фазе (Stage 13 в
  обновлённой нумерации — Public Philosophy).

### Дальше

Stage 8 (revised) — `LLM Usage Logging by Stage`. Затем Stage 9
(Memory Worthiness) и Stage 10 (EH Micro-signals) — оба touch'ат
brain/memory, можно объединить параллельно. Затем Stage 11
(Vector Storage Split). Затем Stage 12 (Director/Voice integration
+ CharacterIntegrityOverride wiring). Завершаем Stage 13 (Public
Philosophy + Rebirth narrative + apologetic-wording cleanup) и
Stage 14 (Final SPEC pass).

---

## 0.20.4.improvements.8 — LLM Usage Logging by Stage

Actionable observability: вместо одной агрегированной цифры
"153k tokens сегодня" — per-stage breakdown (extractor / response /
regen / reaction_intent / reflection / mari_memory / opinion_mining /
consistency_check / embed / voice_debug / scenario_runner) + persistent
jsonl-журнал `data/usage/YYYY-MM-DD.jsonl`.

### Что появилось

- `core/llm.py` — `LLM_STAGE_REGISTRY` (frozen) + `_normalize_stage`.
  `chat / chat_strict / chat_with_profile / embed` принимают
  `stage` и `request_id` keyword'ы (default "unknown" / None).
  `_LLM_USAGE_STATS` дополнен `per_stage: dict` bucket'ом.
  `_record_llm_usage` и `_record_llm_error` пишут per-stage метрики
  И аппендят jsonl-row через crash-safe writer. Любая IO-ошибка
  логируется WARNING и не пробрасывается в hot path.
- `config.LLM_USAGE_JSONL_ENABLED = True`, `LLM_USAGE_JSONL_DIR =
  "data/usage"`.
- Callers обновлены: `unified_extractor.extract_all`
  (`stage="extractor"`), `think_with_context`
  (`stage="reaction_intent"`), `response_stage`
  (`stage="response"`), `brain.py` regen (`stage="regen"`),
  proactive reply (`stage="response"`),
  `response_generation::ensure_reply_matches_thought`
  (`stage="consistency_check"`), `special_memory`
  (`stage="mari_memory"`), `opinion_miner` (`stage="opinion_mining"`),
  `reflection_generator` (`stage="reflection"`), `embed`
  (`stage="embed"` default).
- Все production call sites обёрнуты `try/except TypeError` для
  обратной совместимости с существующими тестами, чьи fake-функции
  не принимают `stage` keyword.

### WebUI propagation

- `/api/webui/llm/usage` — уже возвращал весь `_LLM_USAGE_STATS`;
  теперь автоматически включает `per_stage`.
- `/api/webui/llm/usage/by_stage` — новый, отдельный endpoint
  для удобного чтения per-stage bucket'а без mixing'а с общими
  метриками.
- `/api/webui/llm/usage/jsonl_tail?limit=N` — новый, читает
  последние N строк из сегодняшнего jsonl-файла. Query-параметр
  ограничен `[1, 500]`.

### Privacy

Per roadmap contract: jsonl-row несёт ТОЛЬКО метрики +
`stage / model / ok / request_id`. Никогда не записывается:
- prompt text / system prompt;
- raw user text;
- thought / raw_thought;
- api keys / tokens / secrets.

Тест `test_jsonl_row_does_not_contain_user_text_or_messages`
ассертит это явно: даже если Ollama payload содержал `message.content`
или `system` ключи, row не имеет этих ключей и не содержит соответствующих
строк при JSON-сериализации.

### Тесты

`tests/test_llm_usage_logging.py` — 11 кейсов: writes jsonl row
with stage, unknown stage normalizes, default stage is unknown,
record_error writes ok=False with kind, per_stage bucket aggregates,
per_stage separates distinct stages, jsonl disabled via config,
jsonl crash-safe on IO error, jsonl row sanitization, stage
registry has canonical labels, normalize_stage helper behavior.

Suite целиком: **1412 passed** (был 1401 → +11). compileall: OK.

### Что НЕ сделано

- Daily rotation / cleanup для `data/usage/` — отложено. Файлы
  пишутся по дням; ручная очистка пока на стороне оператора.
  В Stage 13 (Vector Memory Split) / Stage 14 можно добавить
  retention helper.
- Аггрегация по неделе / месяцу для дашборда — webui сейчас отдаёт
  raw rows; UI сам считает summaries.
- Cloud LLM (ChatGPT / Claude) integration: текущая реализация
  заточена под Ollama payload shape. Stage 0.20.5+ (FastAPI bridge)
  при необходимости приведёт chat API под единый shape, и
  `_record_llm_usage` будет читать оттуда.

---

## 0.20.4.improvements.9 + .10 — Memory Worthiness + EH Micro-signals

Объединённый batch — оба touch'ат brain/memory subsystem, оба
deterministic (no-LLM), оба используют perception layer как
sourcing'овый сигнал.

### Stage 9 — Maria Memory Worthiness Filter

Roadmap: `mari_memories` забивались мелочью (small-talk, scene
recaps, "ага"). LLM-only gate в `maybe_create_mari_memory` плохо
отсекал такое — тратился токен и память засорялась. Новый pre-LLM
gate решает «стоит ли вообще тратить LLM-вызов».

`core/memory/worthiness.py`:

- `compute_memory_worthiness(user_input, reply, analysis, frame=None,
  recent_topics=())` — pure, no side effects. Возвращает
  `{memory_worthiness, memory_reasons, memory_reject_reasons,
  accepted}`.
- Positive reasons (registered в `POSITIVE_WORTHINESS_REASONS`):
  `personal_fact_added` (0.70), `relationship_change` (0.70),
  `emotionally_significant` (0.65), `recurring_topic` (0.40),
  `promise_or_future_plan` (0.65), `conflict_or_boundary` (0.65),
  `stable_preference` (0.55), `important_admission` (0.65).
- Negative reasons (registered в `NEGATIVE_WORTHINESS_REASONS`):
  `technical_acknowledgement` (-0.70), `small_talk` (-0.45),
  `one_step_question` (-0.30), `scene_recap` (-0.25),
  `random_laughter` (-0.40 to -0.50), `no_stable_fact` (-0.20
  fallback).
- Score clamped к [0,1]. Threshold `MEMORY_WORTHINESS_THRESHOLD =
  0.65`. Single strong positive обычно проходит; small-talk
  отсекается ещё до LLM.

Integration в `core/engine/special_memory.maybe_create_mari_memory`:
дополнительный try-block перед LLM-вызовом. При rejection — `return
None` плюс лог `mari_memory worthiness gate rejected`. Score и
reasons прописываются в analysis dict (`memory_worthiness`,
`memory_reasons`, `memory_reject_reasons`) для debug-видимости.
`MEMORY_WORTHINESS_ENABLED = True` (default). При False — legacy
LLM-only path.

### Stage 10 — Emotional Health Micro-signals

Roadmap: EH state оставался "perfect" даже при повторяющихся
мелких boundary pressure / pet-name addressing too early / teasing
после уже выраженного дискомфорта. Существующие event types
(COLD_REPLY / IGNORED_INTEREST) ловили только очевидно плохие
события — slow drift пропускали.

`core/brain/emotional_health/micro_signals.py`:

- `compute_micro_signal_delta(perception, recent_trigger_kinds,
  relationship_stage)` — pure mapper из `MariaPerception` полей
  (`pressure`, `threat`, `embarrassment`, `wants_distance`,
  `received_as`, `accepts_warmth`) в небольшие axis-deltas
  `{insecurity, hurt, stability}`.
- `apply_micro_signal_delta(state, delta)` — apply'ит дельты к
  `EmotionalHealthState`, clamp'ит к существующим bounds.

Поведение:
- Single light teasing (pressure < 0.5) → no delta. Drama не
  создаётся.
- Повторное pressure (recent_trigger_kinds: 2+ pressure events) →
  insecurity +0.10 + 0.10*repeat, threat +0.05*repeat, stability
  -0.10*repeat.
- Apology (`received_as=comfort_received`) после недавнего pressure
  → insecurity -0.10, stability +0.10 (softens damage).
- Pet-name addressing после уже зафиксированного boundary → +0.10
  insecurity (специальный track).
- Stage modifier: `irritated` ×1.5 (amplifier), `comfort` ×0.7
  (softener), `close` ×0.5 (strong softener).
- All axis-deltas clamped к ±`EH_MICRO_SIGNAL_MAX_DELTA = 0.4`
  (config). Per-axis bounds от existing schema (insecurity 0..100,
  hurt 0..100, stability 0..100).

Config: `EH_MICRO_SIGNALS_ENABLED = True` (master switch),
`EH_MICRO_SIGNAL_MAX_DELTA = 0.4`. Не интегрирован в PersistenceStage
автоматически — типы и helper'ы готовы, wiring в active path придёт
в Stage 12 вместе с Director integration. Сейчас вызывается
явно из тестов / debug.

### Тесты

`tests/test_mari_memory_worthiness.py` (13 кейсов): default threshold,
small_talk rejected, one_step_question, scene_recap, random_laughter,
personal_fact passes, emotional_significance passes, future_promise
passes, boundary_event passes, recurring_topic raises worthiness,
threshold configurable, all emitted reasons registered, pre-gate
integration skips LLM when rejected.

`tests/test_emotional_health_micro_signals.py` (13 кейсов): single
light teasing no drama, repeated pressure creates micro insecurity,
apology reduces damage, comfort state softens, irritated amplifies,
all deltas clamped, pet-name after boundary, apply state, empty
delta noop, disabled via config, None perception, state clamped to
bounds after repeated application, real perception round-trip.

Suite целиком: **1438 passed** (был 1412 → +26). compileall: OK.

### Что НЕ сделано

- Micro-signal events не пишутся в `data/emotional_health_events.jsonl`
  через EmotionalEvent — это offline tier. Решение: micro-signals
  слишком мелкие чтобы засорять event log; в Stage 12 при
  интеграции в PersistenceStage добавим отдельный audit для них в
  analysis dict (как сейчас сделано для memory worthiness).
- Memory worthiness pre-gate сейчас не consum'ит `frame` (передаётся
  None). После Stage 12 wiring через `ctx.perception_frame` будет
  доступен.

### Дальше

Stage 11 (revised) — Vector Memory Storage Split (`data/vector_memory.json`
→ `data/vector_memory/{entries.jsonl, embeddings.npy, index.json}`).
Standalone, larger migration — отдельный коммит. Затем Stage 12 (Director /
Voice integration + CharacterIntegrityOverride wiring). Stage 13
(narrative + apologetic cleanup). Stage 14 (Final SPEC pass).

---

## 0.20.4.improvements.11 — Vector Memory Storage Split (v2)

Single-JSON vector memory разделён на три артефакта под
`data/vector_memory/`:

```
entries.jsonl    — одна metadata-строка на запись (без эмбеддинга)
embeddings.npy   — numpy матрица (N, embedding_dim), one-to-one
                   с порядком строк в entries.jsonl
index.json       — манифест (version=2, embedding_model, count, dim,
                   last_compacted_at)
```

### Зачем

При росте `vector_memory.json` (метаданные + 768-мерные эмбеддинги в
одном JSON-файле) парс становится медленным; cache mtime смягчает,
но не решает. v2 даёт:

- быстрый load metadata без чтения эмбеддингов (jsonl построчно);
- бинарный матричный формат для embeddings (numpy float32);
- атомарные операции через `tmp + os.replace`;
- portable layout для будущих memory-map / FAISS backends.

### Что появилось

`core/memory/vector_storage_v2.py`:

- `append_entry(text, embedding, *, category, metadata,
  embedding_model)` — добавляет одну запись. Перезаписывает npy
  целиком (OK при текущем cap'е 80 записей).
- `rewrite_all(entries, embeddings, embedding_model)` — atomic full
  rewrite. Используется trim'ом, миграцией, sleep-cycle.
- `migrate_v1_to_v2(v1_path)` — one-way миграция. После успеха
  переименовывает `data/vector_memory.json` → `.json.bak`. Идемпотентна
  (повторный вызов при отсутствии v1 → 0).
- `cosine_search(query_vector, top_k, threshold)` — векторный поиск
  через numpy matrix multiplication (`embeddings @ q / (||e|| ||q||)`).
- `load_entries / load_embeddings / load_index / reset_storage` —
  utilities. `load_entries` skip'ает corrupted jsonl lines silently.
- `Vec2BackendUnavailable` — поднимается, если numpy не импортируется.

`core/memory/vector_memory.py` (router):

- `_v2_enabled()` — True когда `VECTOR_MEMORY_V2_ENABLED=True` И
  numpy importable.
- `_maybe_migrate_v1_to_v2()` — idempotent migration check на каждый
  `add_memory` / `search_memory`. Запускается только если v2-dir не
  существует И legacy v1 файл присутствует.
- `add_memory` / `search_memory` / `replace_last_reply` — пробуют v2
  сначала, при любой exception падают в legacy v1 path.
- `_v2_replace_last_reply` — атомарный rewrite через rewrite_all с
  заменой одной строки.
- `_trim_v2_entries` — та же priority-aware политика drop'а
  (`mari_memory > thought > reply > user`), но операбельна на v2
  row-dicts.

`config.py`: `VECTOR_MEMORY_V2_ENABLED = True` (default). При False
или при отсутствии numpy router transparently degrades к v1.

### Privacy / boundary

- `entries.jsonl` несёт `text` (тот же `safe_excerpt(text, 240)` что и
  v1) и metadata; никаких новых полей с raw data.
- `embeddings.npy` — float32 матрица; нечитаемая глазами, никаких
  secrets / api keys / tokens.
- `index.json` — короткий manifest с числом записей и dim'ом.
- v1 → v2 миграция переносит `vector_memory.json` в
  `vector_memory.json.bak` (вне gitignore'а `data/`, но всё ещё
  внутри ignored `data/` дерева). Public branch не задевается —
  `data/` в .gitignore.

### Тесты

`tests/test_vector_memory_storage_v2.py` — 15 кейсов: v2 dir starts
empty, append writes jsonl row + embedding row, index count matches
shape, search returns top matches, corrupted jsonl line skipped,
migration creates v2 artifacts, migration idempotent, rewrite_all
atomic, router uses v2 when enabled, router falls back to v1 when
disabled, search_memory routes through v2, trim keeps priority
categories, index records embedding_model name, replace_last_reply
routes through v2, v2 disabled when numpy unavailable.

Три legacy теста (`test_vector_memory_cache.py`,
`test_step19_memory_topic_repeat_fixes.py`,
`test_step20_data_hygiene.py`) пиннят v1 internals — добавлен
`monkeypatch.setattr(config, "VECTOR_MEMORY_V2_ENABLED", False)`
чтобы legacy path продолжал тестироваться без regression. v2 path
покрыт отдельно.

Suite целиком: **1453 passed** (был 1438 → +15). compileall: OK.

### Что НЕ сделано

- Memory-mapped загрузка для очень больших матриц — отложена. При
  росте N > 5_000 переключим на `np.memmap` без изменения внешнего
  API.
- Sharding `embeddings.npy` по тысячам строк — отложен. Текущий
  cap MAX_VECTOR_MEMORIES = 80; full rewrite на каждый append
  дёшев.
- Daily retention для backup'а v1 (`vector_memory.json.bak`) —
  оставлен на ручную очистку оператора.

### Дальше

Stage 12 (revised) — Director / Voice integration + CharacterIntegrityOverride
wiring. Затем Stage 13 (philosophy / narrative / apologetic cleanup) и
Stage 14 (final SPEC pass).

---

## 0.20.4.improvements.12 — Director / Voice Integration + Override wiring + EH micro-signal wiring

Самый большой stage из всей линии — wiring всех типов и helper'ов из
Stages 1-11 в active turn flow.

### DirectorManager расширение

`core/engine/director_manager.py`:

- `DirectorInput.perception_frame` — Optional Actor Model frame.
  Типизировано loose (`Any`) чтобы избежать circular import с
  `core.perception`.
- `DirectorDecision` получил пять Actor-Model полей:
  `director_tags / affordances / avoid / hard_constraints /
  integrity_override`.
- `decide()` мерджит frame fields в decision и дополнительно эмитит
  hint'ы `director_tag:<name>` для каждого director_tag — старые
  hint-consumer'ы (prompt builder, debug panel) видят их без отдельной
  wiring.
- `DirectorDecision.to_dict()` сериализует все Actor Model поля для
  debug payload'ов.

### Voice Reaction Tags

`core/voice/orchestration/reaction.py::build_voice_reaction_tags`:

- Новый optional kwarg `perception_frame`. Когда передан, его
  `voice_tags` (`soft / slower / firmer / warmer / ...`) абсорбируются
  в `delivery_tags`. Persona profile продолжает работать рядом —
  это additive merge.
- Существующие callers без `perception_frame` работают без изменений.

### Literal Confusion Guard

`core/engine/literal_confusion_guard.py` (новый):

- `maybe_guard_literal_confusion(reply, perception_frame)` —
  deterministic post-process guard. Срабатывает когда frame несёт
  `do_not_answer_literally` в `perception_tags` ИЛИ `literal_confusion`
  в `avoid`, И reply попадает в generic "не понимаю / что ты имеешь
  в виду / не поняла" паттерн.
- При сработке заменяет reply на одну из трёх character-neutral
  коротких фраз ("Не уверена, что хочу это разбирать прямо сейчас"
  и т.д.). Выбор фразы детерминированный по hash'у оригинала —
  одна и та же offending реплика → одна и та же подстановка.
- Закрывает канонический weak case roadmap'а:
  "Но моя же, верно?)" → "Не понимаю, что ты имеешь в виду" больше
  невозможен в active path'е.

Wired в `core/engine/stages/response_stage.py::generate_reply` —
сразу после `post_process_reply`, до `maybe_ensure_reply_matches_thought`.
Аудит пишется в analysis (`literal_confusion_guard_applied`,
`literal_confusion_guard_reason`).

### EH Micro-signals wiring в PersistenceStage

`core/engine/stages/persistence_stage.py` (раздел 12.7, после legacy
EH audit):

- Если `ctx.perception_frame` непуст И
  `config.EH_MICRO_SIGNALS_ENABLED=True`:
  - Реконструирует perception-view из frame'а (pressure из
    `director_tags`, threat из `avoid`, received_as из
    `perception_tags`).
  - Вычисляет относительный stage label из текущего affection /
    trust / irritation.
  - Берёт `recent_trigger_kinds` с MariaBrain ring buffer.
  - Считает `compute_micro_signal_delta`, применяет через
    `apply_micro_signal_delta` к `EmotionalHealthState`, сохраняет.
  - Audit в `analysis["emotional_health_micro_signal"]` (delta + reasons).
- Любая ошибка → audit остаётся `None`, persistence не падает.

### TurnExecutor payload

`core/engine/turn_executor.py`:

- Новое поле в response payload — `perception_frame` (safe summary).
  Структура: `{scene_tags, perception_tags, affordances, avoid,
  hard_constraints, voice_tags, director_tags, response_guidance,
  confidence}`. Все списки registered identifier'ов + один float.
  Никогда не несёт raw user text / thought / system prompt /
  raw perception internals (`embarrassment / threat /
  wants_distance`).
- Когда frame пустой/отсутствует, dict-shape сохраняется с пустыми
  списками — клиенты могут полагаться на наличие ключей.

### CharacterIntegrityOverride

`DirectorDecision.integrity_override` — slot для one-turn override
request. Сейчас в active path'е не fill'ится (default `None`); типы
и Director gate готовы для будущего опционального wiring'а из
persona profile / response policy. Hard constraint безопасность
обеспечена в самом dataclass'е + `authorize_soft_override`.

### Тесты

`tests/test_reaction_tags_director_integration.py` — 12 кейсов:

- Director: decision surfaces all five Actor Model fields; no
  frame → empty fields; `to_dict` exposes all new keys.
- Voice: `build_voice_reaction_tags` absorbs frame voice_tags;
  caller без frame'а работает без изменений.
- Literal confusion guard: substitutes in teasing scene; does NOT
  fire без marker tag; leaves non-confusion replies; handles None
  frame; deterministic.
- Payload: chat response includes perception_frame summary with
  expected keys + hard_constraints present.

Suite целиком: **1465 passed** (был 1453 → +12). compileall: OK.

### Privacy / contracts inariants

- TurnExecutor payload содержит `perception_frame` safe-summary —
  только registered identifiers. Никаких raw MariaPerception internals
  (`embarrassment / threat / wants_distance`).
- `DirectorDecision.to_dict()` — public; sanitized; consumed
  webui_debug.
- `literal_confusion_guard` substitution фраз — generic, не
  character-specific. Persona profile далее свободен customize'нуть
  через persona profile + prompt block.
- EH micro-signal wiring использует только public frame fields
  (`director_tags`, `avoid`, `perception_tags`) для реконструкции
  perception-view. Никакого доступа к `MariaPerception` internals
  напрямую.
- `core/perception/*` всё ещё не импортирует `core.personas.*`.
  Director / Voice — engine layer — могут импортировать perception,
  но не personas (personas инжектируются через
  `resolve_active_persona_profile`).

### Что НЕ сделано (и почему)

- `CharacterIntegrityOverride` активный wiring в Director.decide
  отложен. Требует persona profile data о soft override preferences
  + Director policy для authorization. Sticker для будущего stage'а.
- Affordance choice (`choose_affordance`) программный wiring отложен.
  LLM продолжает выбирать affordance из prompt block'а сам — natural
  variation. Программный picker нужен для Unity bridge / deterministic
  reply path; не сейчас.
- Voice manager subsystem direct consumption `perception_frame` —
  отложено. Сейчас reaction.py принимает frame, но composition
  layer (`VoiceManager`) пока не передаёт frame downstream. Wiring
  придёт с Unity / Live2D bridge в 0.20.5.

### Дальше

Stage 13 (revised) — Public Philosophy / Rebirth narrative /
apologetic-wording cleanup. Это документация и README work из
Final Corrections doc (§7-13). Затем Stage 14 — Final Bilingual
SPEC pass.

---

## 0.20.4.improvements.13 — Public Philosophy / Rebirth narrative

Документация и narrative — без code changes. Применяет §7-13 из
Final Corrections doc:

### Что появилось

`docs/PROJECT_PHILOSOPHY.md` (новый, ~200 строк):

- **Rebirth narrative** — почему следующая глава отличается от
  New Horizon. Actor model как core principle. «Не строй ещё одну
  клетку. Построй сцену.»
- **Public / private boundary** — короткое позиционирование +
  ссылки на технические doc'и (`docs/public_private_boundary.md`,
  `PERSONAS_SPEC.md`).
- **Дисклеймер для ревьюеров** — verbatim из corrections doc §11:
  «Это не просто техническая витрина. Это дневник путешествия.»
- **Author afterword: «для тех, кто заглянул глубже»** — verbatim из
  §12. Личный авторский текст о детских концептах, амбициях,
  безумии-как-фиче.
- **Author afterword: «о цене разработки»** — verbatim из §13.
  Обращение к молодым разработчикам.
- Связанные документы — ссылки на DEV_LOG, Project_state, SPEC'ы.

`README.md` (dev backend):

- Заменил apologetic-toned заглавную врезку («авторское видение и
  личная мотивация автора проекта... документ объясняет почему
  Maria построена...») на confident framing + ссылку на новую
  philosophy doc.
- Добавил `## Rebirth` секцию вверху, до Current status — пять
  bullet'ов про сдвиг (perception Maria / scene affordances /
  actor model / persona profiles / memory hygiene / public-private
  boundary) + короткое определение `LLM = actress / Persona = role
  / Engine = the stage and its rules`.

Никаких code changes. Тесты не задеты. Suite остаётся **1465 passed**.

### Что НЕ сделано в этом коммите (отложено до отдельной фазы)

- Перенос старого `DEV_LOG.md` в `docs/history/PROJECT_HISTORY_NEW_HORIZON.md`.
  Corrections doc явно: «After starting the Rebirth branch» — это
  делается при создании нового branch'а Rebirth, не сейчас (мы всё
  ещё в New Horizon-линии).
- README EN-зеркало `docs/PROJECT_PHILOSOPHY_EN.md` — отложено.
  RU-вариант первичен; EN придёт с Stage 14 SPEC pass'ом если решим.
- Mirror на public ветку — следующим коммитом, с extra-care по
  privacy boundary'и: проверка отсутствия Cyrillic personal name
  literals, отсутствия раскрытия персонажных предпочтений, no leak
  of private opinion topics.

### Дальше

Public branch mirror — apologetic-wording cleanup + Rebirth section
+ philosophy doc. Затем Stage 14 — Final Bilingual SPEC pass.

---

## 0.20.4.improvements.14 — Final Bilingual SPEC Pass

Documentation-only stage. Synchronizes EN SPEC mirrors with the RU
deltas accumulated during Stages 1-13. Verifies SPEC/EN coverage
across the whole tree.

### Что обновлено

Полный rewrite (RU и EN сильно разъехались):

- `core/perception/PERCEPTION_SPEC_EN.md` (76 → 324 строки):
  Actor Model, text_signals, integrity_override, affordance_choice,
  Stage 1-12 mapping table, privacy boundary, persona profile
  contract, invariants. Полностью зеркалит текущий RU SPEC.

Точечные дополнения:

- `core/personas/PERSONAS_SPEC_EN.md` — добавлены секции «Boundary
  with perception» (Stage 2) + «Public template safety».
- `core/engine/ENGINE_SPEC_EN.md` — file map включил
  `reaction_intent.py`, `literal_confusion_guard.py`,
  `prompt_blocks/date_context.py`; обновлён invariants блок
  (`LEGACY_FREE_TEXT_REACTION_INTENT_ENABLED`, scene-affordance
  prompt block, literal-confusion guard).
- `core/brain/BRAIN_SPEC_EN.md` — добавлена секция «Emotional Health
  micro-signals (Stage 0.20.4.improvements.10)».
- `core/memory/MEMORY_SPEC_EN.md` — добавлены секции «Maria memory
  worthiness filter (Stage 9)» и «Vector memory storage v2
  (Stage 11)»; реорганизованы хвостовые секции.

Test count update в RU+EN:

- `tests/TESTS_SPEC.md`, `tests/TESTS_SPEC_EN.md` — baseline
  обновлён с `1250 passed` на `1465 passed` + summary стейджей.
- `README.md` (dev) — обновлён Test baseline в Current status.

### SPEC/EN coverage

Все RU SPEC'и имеют EN-зеркала (17/17):

```
apps/APPS_SPEC ↔ APPS_SPEC_EN
core/CORE_SPEC ↔ CORE_SPEC_EN
core/brain/BRAIN_SPEC ↔ BRAIN_SPEC_EN
core/capabilities/CAPABILITIES_SPEC ↔ CAPABILITIES_SPEC_EN
core/engine/ENGINE_SPEC ↔ ENGINE_SPEC_EN
core/memory/MEMORY_SPEC ↔ MEMORY_SPEC_EN
core/perception/PERCEPTION_SPEC ↔ PERCEPTION_SPEC_EN
core/personas/PERSONAS_SPEC ↔ PERSONAS_SPEC_EN
core/runtime/RUNTIME_SPEC ↔ RUNTIME_SPEC_EN
core/social/SOCIAL_SPEC ↔ SOCIAL_SPEC_EN
core/voice/VOICE_SPEC ↔ VOICE_SPEC_EN
core/voice/orchestration/ORCHESTRATION_SPEC ↔ ORCHESTRATION_SPEC_EN
core/voice/playback/PLAYBACK_SPEC ↔ PLAYBACK_SPEC_EN
core/voice/stt/STT_SPEC ↔ STT_SPEC_EN
core/voice/tts/TTS_SPEC ↔ TTS_SPEC_EN
core/voice/vad/VAD_SPEC ↔ VAD_SPEC_EN
tests/TESTS_SPEC ↔ TESTS_SPEC_EN
```

### Зачем стейдж нужен

Roadmap §13 (revised Stage 14): «Final bilingual SPEC pass».
Каждый закрытый stage обновлял RU SPEC сразу; EN-зеркала отставали.
Этот pass закрывает дельту так, чтобы публичные ревьюеры читали
актуальный EN-контент.

### Suite

**1465 passed** (без изменений — stage docs-only). compileall: OK.

### Что осталось из roadmap'а

Линия `0.20.4.improvements` закрыта. Все 13 стейджей по revised
numbering применены к коду + документации.

Ветка `direction/new-horizon-0.20.4.x-improvements` готова к merge'у
в основную линию или к продолжению через будущую `rebirth/*` ветку.

Полный итог:

```
1. Maria Perception Tags Foundation              ✅
2. Persona Trigger Profiles                       ✅
3. Hidden Thought → Structured Reaction Tags     ✅
4. Actor Model / Scene Affordances /             ✅
   Character Integrity Override
5. Teasing / Flirt / Pet-name Calibration        ✅
6. Opinion Belief Separation                      ✅
7. Runtime Date Context                           ✅
8. LLM Usage Logging by Stage                    ✅
9. Maria Memory Worthiness Filter                ✅
10. Emotional Health Micro-signals                ✅
11. Vector Memory Storage Split                   ✅
12. Reaction Tags → Director / Voice Integration ✅
13. Public Philosophy / Rebirth narrative        ✅
14. Final Bilingual SPEC Pass                     ✅
```

Test count: 1295 → **1465** (+170 фокус-кейсов).

Главный концептуальный сдвиг применён: from rigid commands to
scene affordances; LLM = actress / persona = role / engine = the
stage and its rules.
