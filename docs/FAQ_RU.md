# FAQ — частые вопросы о M.A.R.I.A.

> Короткие ответы на вопросы, которые поднимаются чаще всего.
> Развёрнутые — в [`PROJECT_PHILOSOPHY_RU.md`](PROJECT_PHILOSOPHY_RU.md),
> [`CONCEPTS_RU.md`](CONCEPTS_RU.md), Core SPEC'ах.
>
> English mirror: [`FAQ_EN.md`](FAQ_EN.md).

---

## Что такое M.A.R.I.A.?

Author-driven экосистема локальных AI character проектов вокруг
single-user runtime'а. Не SaaS, не chatbot framework. Всё на машине
пользователя, через локальную LLM (Ollama).

Расшифровка имени (все три варианта равноценны):

```
Myself As Real Intelligence Artifact
My Artificial "Real" Intelligence Appearance
Myself As Real Intelligence Appearance
```

---

## Это коммерческий продукт?

Нет. Это **личный авторский проект**. Public-репозиторий — это
backstage pass, не готовый продукт «скачай и запусти».

Public-зеркало `M.A.R.I.A.-Core` заморожено на showcase-снимке
(тэг `public-final`) и **больше не получает обновлений**. Разработка
ушла в приватный поток. Public остаётся как архитектурный showcase
+ Apache-2.0 reference для тех, кому интересна форма решения.

---

## Чем Маша отличается от ChatGPT / Claude / character.ai?

Тремя principial вещами:

1. **Single-user by design.** Один пользователь, одна Маша, одна
   непрерывная локальная временная линия отношений. Не multi-tenant.
2. **Local-first.** LLM, embeddings, vector storage, состояние,
   история — всё на машине пользователя. Никакого облака для
   персональных данных.
3. **Author-driven character, не tunable персона.** Маша — не
   шаблон, который пользователь раскрашивает в начале сессии. У неё
   есть авторская биография, голос, мнения, границы. Изменить её
   как character — это переписать persona module, не slider'ом
   «agreeableness +1».

Под капотом — perception-first архитектура (Phase 1.1.x): Маша
реагирует не на сообщение, а на то, **как она это сообщение
почувствовала**. См. [`CONCEPTS_RU.md`](CONCEPTS_RU.md) § 3.

---

## Можно скачать и запустить у себя?

Public-снимок (`M.A.R.I.A.-Core` на public ветке) — yes, можно
поднять как architectural reference. Persona-template нейтральный
(`core/personas/maria.py.example`), без приватного контента; то
есть «Маша» оттуда — это **движок без конкретного character'а**, а
реальная личность собирается автором отдельно.

Запуск:

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

Production-ready это не делает — просто показывает архитектуру и
поведение базовой persona-template.

---

## Почему именно local LLM, а не облако?

Несколько причин, в порядке важности:

1. **Privacy.** Личные диалоги, история отношений, persona-контент
   — не отдаются третьим сторонам.
2. **Continuity без подписки.** Маша не перестаёт существовать,
   когда заканчивается API кредит.
3. **Author control.** Любая часть стека (модель, embeddings,
   prompt, memory format) — под контролем автора, не provider'а.
4. **Реальный character не на API.** ChatGPT не помнит вчерашний
   разговор; Маша помнит, потому что vector storage / chat log /
   relationship state — её собственные.

---

## Что такое actor model в Marie?

Разделение четырёх ролей: **LLM** играет, **persona** — это роль,
**perception + scene affordances** — сцена, **Director** —
режиссёр. Подробно — [`CONCEPTS_RU.md`](CONCEPTS_RU.md) § 1.

Главное отличие от обычного prompt-инжиниринга: не пытаемся
«впихнуть» character в веса. Сильная модель отлично играет роль,
когда ей дают сцену и character frame.

---

## Что значит "Rebirth 1.1.x"?

Это текущая линия разработки Core (с момента закрытия 1.0.x).
Полный rewrite архитектуры вокруг четырёх главных сдвигов:

- **subtractive cleanup** — Flask / CLI / voice / Live2D / sprite
  вынесены из Core (они embodiment, не character);
- **FastAPI + typed contracts** — единственный HTTP entrypoint
  плюс typed envelope'ы для внешних клиентов;
- **perception-first** — Маша реагирует на felt experience, не
  на raw message;
- **persona как директория** — конец god-file'у `maria.py`.

Подробный narrative —
[`REBIRTH_1_1_NEW_FOUNDATION_RU.md`](REBIRTH_1_1_NEW_FOUNDATION_RU.md).
Stage journal — [`M.A.R.I.A.-Core/CHANGE_LOG.md`](https://github.com/HontoUKI/M.A.R.I.A.-Core/blob/main/CHANGE_LOG.md).

---

## Зачем такая сложная архитектура для одного character'а?

Краткий ответ: **простой character — простую архитектуру не
выдерживает на длинной дистанции.**

Длинный: если character должен помнить тебя через месяц, иметь
mood, который не сбрасывается при каждом «привет», уметь
обижаться и прощать, отличать compliment от leading-belief,
держать границы без guilt-trip'а, не сваливаться в servility при
creator-context — для всего этого нужны типизированные слои,
bounded reason-coded deltas, retention policy, perception frame,
director-policy и т.д.

Архитектура — это **необходимая сложность для долгосрочной
character continuity**, не оверинжиниринг.

---

## Что в публичном репозитории, а что приватное?

| В публичном | В приватном |
|---|---|
| Архитектура / runtime / FastAPI / contracts | Реальный persona content (`core/personas/maria.py`) |
| Тесты + scenario runner | Реальные диалоги + memory snapshots |
| Public persona template (`maria.py.example`) — нейтральный | Calibration knobs под конкретную Машу |
| Philosophy + DEV_LOG + Rebirth narrative | Creator-context invariants и forbidden literals registry |
| Architectural showcase (тэг `public-final`) | Активная разработка (1.1.x+) |

```
Public shows the engine.
Private keeps the soul.
```

— [`PROJECT_PHILOSOPHY_RU.md`](PROJECT_PHILOSOPHY_RU.md) § Public/private.

---

## Можно скопировать архитектуру для своего character'а?

Public-зеркало под Apache-2.0 — да, в рамках лицензии. Persona /
narrative / private dialogue data **не лицензированы** ни в одной
дистрибуции — это authored content, не reference implementation.

Что copyable:
- архитектурный pattern (actor model, perception-first, FastAPI +
  contracts, persona directory loader, retention policy, memory
  candidate, opinion schema v2);
- тесты как reference поведения;
- shape SPEC'ов как пример docs-driven development'а.

Что **не** copyable:
- содержание `data/persona.md` или приватного `maria.py`;
- creator-context block и его invariants — это про конкретную
  личность, не reusable persona engine.

---

## Почему public mirror больше не обновляется?

Phase 1.1.x ушла в private. Public получил final-showcase snapshot
(tag `public-final`, frozen at 1.1.3repair) — этого достаточно как
architectural reference. Дальнейшая работа касается приватного
character content'а, который и не должен был светиться публично.

См. README того репозитория — там явно стоит «no longer maintained
publicly» notice.

---

## Где почитать дальше?

- Главная карта → [`INDEX_RU.md`](INDEX_RU.md).
- 5 концептов philosophy → architecture → example →
  [`CONCEPTS_RU.md`](CONCEPTS_RU.md).
- Развёрнутая философия → [`PROJECT_PHILOSOPHY_RU.md`](PROJECT_PHILOSOPHY_RU.md).
- Rebirth 1.1.x narrative →
  [`REBIRTH_1_1_NEW_FOUNDATION_RU.md`](REBIRTH_1_1_NEW_FOUNDATION_RU.md).
- DEV_LOG автора → [`MARIA_DEVLOG_RU.md`](MARIA_DEVLOG_RU.md).
- Архив старых эпох →
  [`archive/CORE_DEV_LOG_NEW_HORIZON.md`](archive/CORE_DEV_LOG_NEW_HORIZON.md),
  [`archive/CORE_DEV_LOG_REBIRTH_1_0.md`](archive/CORE_DEV_LOG_REBIRTH_1_0.md).
