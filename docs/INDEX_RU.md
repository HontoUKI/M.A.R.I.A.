# M.A.R.I.A. — навигационный hub

> Точка входа в экосистемную документацию M.A.R.I.A. Это **карта
> смыслов и материалов**, а не SPEC и не roadmap.
>
> English mirror: [`INDEX_EN.md`](INDEX_EN.md).

---

## С чего начать

| Если ты... | Иди в |
|---|---|
| Здесь впервые и хочешь короткий ответ «что это вообще такое» | [`FAQ_RU.md`](FAQ_RU.md) |
| Хочешь понять **зачем** проект существует | [`PROJECT_PHILOSOPHY_RU.md`](PROJECT_PHILOSOPHY_RU.md) |
| Хочешь увидеть **как философия стала кодом** | [`CONCEPTS_RU.md`](CONCEPTS_RU.md) |
| Хочешь технический roadmap текущей линии | [`REBIRTH_1_1_NEW_FOUNDATION_RU.md`](REBIRTH_1_1_NEW_FOUNDATION_RU.md) |
| Хочешь личный дневник автора | [`MARIA_DEVLOG_RU.md`](MARIA_DEVLOG_RU.md) |
| Хочешь архив того, как до этого добрались | `archive/` ниже |
| Хочешь поднять архитектурный showcase локально | [`FAQ_RU.md` → «Можно скачать и запустить»](FAQ_RU.md#можно-скачать-и-запустить-у-себя) |
| Хочешь нырнуть в технические SPEC'ы | [`M.A.R.I.A.-Core`](https://github.com/HontoUKI/M.A.R.I.A.-Core) |

---

## Reading paths (рекомендованные маршруты)

### Маршрут «Я тут впервые, у меня 10 минут»

1. [`README_RU.md`](../README_RU.md) — что вообще такое M.A.R.I.A.
2. [`FAQ_RU.md`](FAQ_RU.md) — ответы на основные вопросы.
3. Один концепт по выбору из [`CONCEPTS_RU.md`](CONCEPTS_RU.md) —
   например § 1 (actor model) или § 3 (perception-first).

### Маршрут «Хочу понять идею»

1. [`PROJECT_PHILOSOPHY_RU.md`](PROJECT_PHILOSOPHY_RU.md) —
   полная философия, Rebirth narrative, public/private boundary,
   author afterword.
2. [`CONCEPTS_RU.md`](CONCEPTS_RU.md) — 5 концептов с примерами:
   как философия превращается в архитектуру.
3. [`REBIRTH_1_1_NEW_FOUNDATION_RU.md`](REBIRTH_1_1_NEW_FOUNDATION_RU.md)
   — нарратив текущей линии.

### Маршрут «Хочу понять архитектуру»

1. [`CONCEPTS_RU.md`](CONCEPTS_RU.md) — концепты как entry point.
2. [`REBIRTH_1_1_NEW_FOUNDATION_RU.md`](REBIRTH_1_1_NEW_FOUNDATION_RU.md)
   — общая форма Core 1.1.x.
3. Технический roadmap →
   [`M.A.R.I.A.-Core/docs/roadmap/REBIRTH_1_1_NEW_FOUNDATION.md`](https://github.com/HontoUKI/M.A.R.I.A.-Core/blob/main/docs/roadmap/REBIRTH_1_1_NEW_FOUNDATION.md).
4. Активные пакетные SPEC'ы → `M.A.R.I.A.-Core/core/*/__SPEC__.md`.

### Маршрут «Хочу историю»

1. [`MARIA_DEVLOG_RU.md`](MARIA_DEVLOG_RU.md) — авторский DevLog.
2. [`archive/CORE_DEV_LOG_NEW_HORIZON.md`](archive/CORE_DEV_LOG_NEW_HORIZON.md)
   — журнал эпохи 0.x.
3. [`archive/CORE_DEV_LOG_REBIRTH_1_0.md`](archive/CORE_DEV_LOG_REBIRTH_1_0.md)
   — журнал линии Rebirth 1.0.x.

---

## Карта документов

### Философия и направление

| Документ | Что внутри |
|---|---|
| [`PROJECT_PHILOSOPHY_RU.md`](PROJECT_PHILOSOPHY_RU.md) | Rebirth narrative, actor model framing, public/private boundary, author afterword, «о цене разработки». |
| [`REBIRTH_1_1_NEW_FOUNDATION_RU.md`](REBIRTH_1_1_NEW_FOUNDATION_RU.md) | Экосистемный narrative текущей Core-линии. Почему subtractive rewrite, что владеет Core, что — внешние клиенты. |
| [`MARIA_DEVLOG_RU.md`](MARIA_DEVLOG_RU.md) | Авторская хроника развития проекта. |

### Прикладные документы (новые)

| Документ | Что внутри |
|---|---|
| [`CONCEPTS_RU.md`](CONCEPTS_RU.md) | 5 концептов philosophy → architecture → example. Главное место чтобы увидеть как идеи становятся кодом. |
| [`FAQ_RU.md`](FAQ_RU.md) | Короткие ответы на 10 самых частых вопросов. |

### Архив (закрытые эпохи)

| Документ | Что внутри |
|---|---|
| [`archive/CORE_DEV_LOG_NEW_HORIZON.md`](archive/CORE_DEV_LOG_NEW_HORIZON.md) | DEV_LOG эпохи `0.1.x` – `0.20.4.x` (New Horizon). |
| [`archive/CORE_DEV_LOG_REBIRTH_1_0.md`](archive/CORE_DEV_LOG_REBIRTH_1_0.md) | DEV_LOG линии Rebirth 1.0.x (foundation modules). |

---

## Внешние ресурсы

### Репозитории экосистемы

| Repo | Назначение | Статус |
|---|---|---|
| [`M.A.R.I.A.-Core`](https://github.com/HontoUKI/M.A.R.I.A.-Core) | Public sanitized snapshot Core runtime'а (Apache-2.0). | Frozen at `public-final` (1.1.3repair) — больше не обновляется. |
| [`M.A.R.I.A.-WebUI`](https://github.com/HontoUKI/M.A.R.I.A.-WebUI) | Debug/developer cockpit. | Public; ожидает миграции на новый FastAPI контракт. |

Будущие модули экосистемы (планируются как отдельные клиенты): Voice,
CV, Avatar/Live2D, sprite, Discord — все вне Core, через
[`contracts/`](https://github.com/HontoUKI/M.A.R.I.A.-Core/tree/main/contracts)
typed envelope'ы.

### Где живёт что

```
Этот репо (M.A.R.I.A.)        — философия, ecosystem map, public direction,
                                history archive, narrative docs.
M.A.R.I.A.-Core                — runtime, API, FastAPI, contracts, memory,
                                state, perception, tests, setup,
                                Core-specific Project_state и CHANGE_LOG.
M.A.R.I.A.-WebUI               — debug cockpit (separate session).
```

---

## Хочу контрибьютнуть?

Public-зеркало Core заморожено и не принимает PR'ы. M.A.R.I.A.
ecosystem hub (этот репо) принимает PR'ы по docs / philosophy /
narrative если они вписываются в author direction; пишите issue
перед PR'ом.

Активная character-разработка — приватная по дизайну. Это authored
project, не open community.

---

## Карта концептов одним взглядом

```
Философия                       Архитектурное решение               Пример
─────────────────────────────   ──────────────────────────────      ───────────────────────────
"LLM играет Маршу, а не есть"   persona dir + Director +            AFFORDANCE_PERSONA_WEIGHTS
                                affordance choice                   + recency rotation
"Core is Maria"                 FastAPI shell + contracts/          SceneSnapshot POST contract
                                + external clients                  + chosen_affordance out
"Реагирует на чувство"          FeelingFrame + StateDeltaProposal   reason-coded bounded delta
"Worthy memory защищена"        MemoryCandidate + RetentionPolicy   routing table + drop-honor
"Relationships ≠ authority"     creator-context block + forbidden   0 servile / 0 guilt / 0 leak
                                literals tripwire                   scenario invariants
```

Подробно — [`CONCEPTS_RU.md`](CONCEPTS_RU.md).
