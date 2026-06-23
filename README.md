# M.A.R.I.A.

<div align="center">

[🇷🇺 Русская версия README](README_RU.md)

![Status](https://img.shields.io/badge/status-active-success?style=for-the-badge)
![Architecture](https://img.shields.io/badge/architecture-author--driven-blue?style=for-the-badge)
![Runtime](https://img.shields.io/badge/runtime-local%20AI-black?style=for-the-badge)
![Core](https://img.shields.io/badge/M.A.R.I.A.-Core-8A2BE2?style=for-the-badge)

</div>

---

> **Where this stands right now.** At its current stage the Core architecture is an attempt to
> simulate a personality with an **"inner core"** — not a product, not a SaaS. It is a
> **single-user system that brings to life exactly one authored personality, born in dialogue**.
> (You could, of course, craft your own "Maria" if you wanted — but authoring a personality is a
> labor-intensive process.)
>
> 👉 **See her in motion:** [a sanitized glimpse of one clean run](public/showrun) — conversation, diary and reflection.

---

## What is M.A.R.I.A.?

**M.A.R.I.A.** is an author-driven ecosystem of local AI character projects built around `M.A.R.I.A.-Core`.

The name has several intentional interpretations:

```text
M.A.R.I.A.
├─ Myself As Real Intelligence Artifact
├─ My Artificial “Real” Intelligence Appearance
└─ Myself As Real Intelligence Appearance
```

All interpretations reflect different aspects of the project and are considered valid.

---

## Official repositories

| Repository | Description |
|---|---|
| [`M.A.R.I.A.-Core`](https://github.com/HontoUKI/M.A.R.I.A.-Core) | Canonical single-user runtime and architectural heart of the ecosystem. The public repository is an early **sanitized snapshot**; active development is author-driven and private (see *Project status* below). |
| [`M.A.R.I.A.-Voice`](https://github.com/HontoUKI/M.A.R.I.A.-Voice) | External local voice runtime: STT, TTS, VAD, playback and diagnostics |
| [`M.A.R.I.A.-WebUI`](https://github.com/HontoUKI/M.A.R.I.A.-WebUI) | *(retired)* Early debug/developer cockpit. Frozen and compatible only with the pre-refactoring Core; kept for historical reference. |

The architecture has since grown beyond these public repositories — a dedicated local **world-runtime** (perception & interaction), an **ML-driven perception brain**, a **cognitive memory / dossier** layer and a **controlled-agency** layer. Those modules are developed privately for now; this hub tracks their direction conceptually (see *Project status*).

A **Presence-Shell** desktop client (Tauri — avatar rendering + window/audio bridge, replacing earlier Unity/Live2D plans) is built; CV (presence observer) and other clients remain future ideas.

Repositories using the `M.A.R.I.A.` naming but not listed here are not considered official parts of the ecosystem.

---

## Reading order

This repository is not a runtime repository.  
It is the philosophical, ecosystem and documentation entry point of M.A.R.I.A.

**Start here:** [`docs/INDEX_EN.md`](docs/INDEX_EN.md) — the navigation hub with reading paths, document map, FAQ and concept arcs.

Recommended reading paths:

1. `README.md`
2. [`docs/INDEX_EN.md`](docs/INDEX_EN.md) — navigation hub
3. [`docs/FAQ_EN.md`](docs/FAQ_EN.md) — frequently asked questions
4. [`docs/PROJECT_PHILOSOPHY_EN.md`](docs/PROJECT_PHILOSOPHY_EN.md) — full philosophy
5. [`docs/CONCEPTS_EN.md`](docs/CONCEPTS_EN.md) — 5 concepts traced from philosophy → architecture → example
6. [`docs/REBIRTH_1_1_NEW_FOUNDATION_EN.md`](docs/REBIRTH_1_1_NEW_FOUNDATION_EN.md) — the Rebirth 1.1 foundation narrative (the project has since advanced through many further Rebirth lines — see *Project status*)
7. [`docs/MARIA_DEVLOG_EN.md`](docs/MARIA_DEVLOG_EN.md) — author devlog

---

## Philosophy

M.A.R.I.A. is not designed as a generic “AI companion SaaS”.

The ecosystem is built around:

- authored identity;
- long-term continuity;
- presence over utility;
- subjective perception;
- imperfect and non-compliant behavior;
- single-user character runtime philosophy;
- separation between engine and identity.

The goal is not to create a perfect assistant.

The goal is to create a believable local character system capable of long-term interaction, memory, perception and emotional continuity.

---

## Ecosystem structure

```text
M.A.R.I.A.
├─ M.A.R.I.A.-Core               (canonical runtime; public repo = early snapshot)
├─ M.A.R.I.A.-Voice              (active, TTS/STT/VAD/playback)
├─ M.A.R.I.A.-Presence-Shell     (built, Tauri desktop shell —
│                                 avatar rendering + window/audio bridge;
│                                 replaces earlier Unity/Live2D plans)
├─ world-runtime                 (private — perception & interaction layer)
├─ ML perception brain           (private — trains the runtime's brain)
├─ cognitive memory / dossier    (private — meaning & becoming layer)
├─ M.A.R.I.A.-WebUI              (retired — early debug cockpit, frozen)
├─ M.A.R.I.A.-CV                 (future, presence observer)
└─ Other ecosystem modules
```

`M.A.R.I.A.-Core` remains the canonical runtime nucleus of the ecosystem.

---

## About the project

The ecosystem was originally created and is currently maintained by a single author.

M.A.R.I.A. is developed as an author-driven project focused on architecture, behavior systems, runtime continuity and long-term experimentation around local AI characters.

This repository exists as:

- ecosystem map;
- philosophical documentation;
- historical archive of project evolution;
- public entry point into the M.A.R.I.A. ecosystem.

---

## Project status

M.A.R.I.A. is **actively developed**, but development is currently **author-driven and closed-doors**.

What that means in practice:

- The public `M.A.R.I.A.-Core` repository is an early **sanitized snapshot** (frozen at the Rebirth 1.1.x foundation). It does **not** reflect current internals.
- Since that snapshot the runtime has gone through many further **Rebirth lines**: the perception layer was extracted into a dedicated local **world-runtime**; perception is now driven by a self-written **ML brain**; a **cognitive memory / dossier + reflection** layer gives the character meaning and continuity; and a permission-gated **controlled-agency** layer gives her safe "hands". These modules are private for now.
- This hub is the **public window** into that direction — high-level, conceptual, no internal source.

The goal remains the same: a believable, local, single-user character system with long-term memory, subjective perception and emotional continuity — not a generic assistant.

---

## 📊 Code metrics

Production code vs tests across the ecosystem — source languages only (docs, data, generated and vendored files excluded), measured with [tokei](https://github.com/XAMPPRocky/tokei):

| Module | Production | Tests | Test-to-prod |
|---|--:|--:|--:|
| `M.A.R.I.A.-Core` | 26 533 | 21 337 | **80%** |
| `M.A.R.I.A.-Voice` | 2 933 | 1 164 | 40% |
| `Presence-Shell` | 2 141 | — | — |
| Private modules *(world-runtime · ML brain · cognition)* | 11 984 | 2 925 | 24% |
| **Ecosystem total** | **43 591** | **25 426** | **58%** |

_Lines of code (Python · JS / JSX · Rust · CSS). Test-to-prod = test LOC as a share of production LOC._

---

## Following development & future contribution

- **To follow along:** watch / star this repository — direction updates and narrative land here.
- **Contribution is not open yet.** The project is intentionally single-author and closed-doors while the architecture stabilizes.
- **For the future:** collaboration may open later. If the philosophy resonates and you'd want to be involved down the line, the welcome path is to follow the project, read the philosophy/concepts docs, and reach out via the repository (issues/discussions) — no code contributions are expected or accepted at this stage.

---

## Screenshots

> Debug WebUI — early developer/debug cockpit (now **retired**; shown for historical reference)

![Maria Debug WebUI — live conversation](public/screens/maria-live-conversation.png)

![Maria Debug WebUI — empty state](public/screens/maria-empty-backend-off.png)
