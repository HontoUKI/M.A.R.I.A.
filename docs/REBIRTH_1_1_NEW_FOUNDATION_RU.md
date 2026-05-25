# Rebirth 1.1.x — New Foundation (Экосистемный нарратив)

> Ecosystem-level документ направления для проекта **M.A.R.I.A.**.
> Технический roadmap Core живёт в
> [`M.A.R.I.A.-Core/docs/roadmap/REBIRTH_1_1_NEW_FOUNDATION.md`](https://github.com/HontoUKI/M.A.R.I.A.-Core/blob/main/docs/roadmap/REBIRTH_1_1_NEW_FOUNDATION.md).
> Этот документ — философское / направленческое объявление, не
> реализация.

## Зачем New Foundation

Эпоха `0.x` и Rebirth `1.0.x` доказали, что локальный AI character
runtime работает. У него есть память, состояние отношений, мнения,
perception improvements, actor model, scene affordances,
детерминированная offline cognition, эксперименты с voice runtime,
debug WebUI и публичный showcase.

Этого более чем достаточно, чтобы доказать, что идея реальна.

И этого же достаточно, чтобы — задним числом — увидеть, что фундамент
накопил исторический груз: старая extractor-философия,
hidden-thought legacy, fallback-driven поведение, god-file `maria.py`,
voice / Live2D / sprite / CV runtime внутри или рядом с Core,
Flask-совместимость, слишком много несвязанных механик в одном
проекте.

**Rebirth 1.1.x** — момент, когда мы перестаём полировать старый
фундамент и перестраиваем его.

## Главная идея

Старый поток:

```
Что объективно сказал пользователь?
  → Какая intent / emotion классификация?
  → Что должна сделать Мария?
```

Новый поток:

```
Что Мария могла почувствовать после этого сообщения?
  → Какое пространство реакций ей доступно?
  → Как она ответит как Мария?
```

Короткая формула:

```
Сообщение → чувство Марии → пространство реакций → ответ.
```

Жёсткое правило:

```
Мария не реагирует напрямую на сообщение пользователя.
Мария реагирует на то, как она воспринимает и чувствует это сообщение.
```

## Чем становится `M.A.R.I.A.-Core`

```
Ядро — это Мария.
Клиенты — это способы её проявления.
```

`M.A.R.I.A.-Core` становится **single-user FastAPI runtime'ом** с:

- perception-first архитектурой;
- памятью и состоянием отношений;
- persona как директорией документов (без god-file);
- contracts / events на внешней границе;
- без Voice / Live2D / Sprite / CV implementations внутри Core.

Внешние embodiment-модули — отдельные проекты экосистемы, общающиеся
с Core через HTTP / events:

- `M.A.R.I.A.-Voice` (TTS / STT / VAD / playback);
- `M.A.R.I.A.-CV` (камеры / screen observers, отдающие presence
  snapshots);
- `M.A.R.I.A.-Presence-Shell` — embodiment-клиент.
  **Десктопный shell на Tauri** (Rust native + web-слой) — рендер
  аватара через Live2D / sprite внутри webview'а плюс window /
  state / audio мост. Заменяет более ранние раздельные планы
  `M.A.R.I.A.-Avatar` (рендер) и `M.A.R.I.A.-Unity` (полноценный
  embodiment) на один более лёгкий клиент — без game-engine
  overhead'а там, где задача — character rendering плюс window
  management.

Они остаются author-driven и сознательно decoupled — не заброшены.
Они вне Core, потому что им так и положено быть.

## Что остаётся single-user

Core — **single-user by design**. Он владеет:

```
одной Марией
одним основным пользователем
одной непрерывной локальной временной линией отношений
одним приватным character runtime
одной приватной системой памяти
```

Сцены и другие люди обрабатываются как **контекст** — обычно
поступают от внешних клиентов в виде scene snapshot'ов — а не как
multi-tenant graph платформа.

## Что владеет `M.A.R.I.A.` ecosystem hub

Ecosystem-репозиторий (этот) держит:

- философию;
- ecosystem map;
- public direction;
- архив истории (старые DEV_LOG'и эпох New Horizon и Rebirth 1.0.x);
- public / private boundary contract.

`M.A.R.I.A.-Core` держит только техническую Core-документацию: runtime,
API, FastAPI, contracts, memory, state, perception, tests, setup,
Core-specific Project_state и CHANGE_LOG.

## Foundation-уроки, которые забираем с собой

Rebirth 1.0.x закончилась несколькими foundation-модулями, которые
переходят в новую архитектуру:

- typed prompt fragment / recipe registry;
- character-aware capability help modes;
- typed opinion schema v2 (leading-belief никогда не становится Maria
  preference);
- memory retention policy (worthy memories защищены от dialogue
  churn'а);
- programmatic affordance choice (по построению только soft-выбор).

Они были намеренно «foundation-only» в 1.0.x — объявлены и покрыты
тестами, но не подключены. В 1.1.x новая архитектура наконец-то
их консьюмит.

## Чем это не является

Rebirth 1.1.x — **не** rewrite от усталости.

Это rewrite от опыта.

```
Мы не начинаем с нуля.
Мы начинаем с опыта.
```

Старая линия закрыта под тегом `1.0.x-closed`. История, удалённые
модули, ранние эксперименты — всё это остаётся доступным через тег.
Новая ветка имеет право быть subtractive, имеет право удалять код,
убирать модули и отказываться нести старые предположения. История в
безопасности; у будущего есть пространство.

## Порядок чтения

1. Этот документ.
2. `M.A.R.I.A.-Core/docs/roadmap/REBIRTH_1_1_NEW_FOUNDATION.md` —
   технический roadmap с фазами, контрактами и validation gates.
3. `M.A.R.I.A.-Core/Project_state.md` — текущее состояние и список
   cleanup candidates.
4. `M.A.R.I.A.-Core/CHANGE_LOG.md` — active stage journal.

English mirror of this document:
[`REBIRTH_1_1_NEW_FOUNDATION_EN.md`](REBIRTH_1_1_NEW_FOUNDATION_EN.md).
