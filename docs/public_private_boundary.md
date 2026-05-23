# Public / private boundary

> Document version: 0.20.4.0e. Lives in the development branch
> `direction/new-horizon-0.20.4.x`. The sister branch `public/showcase`
> is a sanitized public showcase of the engine — Maria is first a
> personal project, but the public repository also serves as an
> open-source architecture showcase of the engine.

## Source code vs runtime data

| Category | Branch behavior | Examples |
|---|---|---|
| **Source code** | versioned everywhere | `core/`, `apps/`, `tools/`, `tests/` |
| **Engine docs** | versioned everywhere | `docs/architecture/`, `DEV_LOG.md`, `Project_state.md`, `README.md` |
| **Persona contract (template)** | versioned everywhere | `core/personas/__init__.py`, `core/personas/maria.py.example` |
| **Persona concrete (private)** | **dev branch only** | `core/personas/maria.py` |
| **Persona biography (private)** | dev branch tracks it intentionally; never publish | `data/persona.md` |
| **Runtime state (private)** | never versioned | `data/*.json`, `data/history/*.jsonl` |
| **Secrets / env** | never versioned; `.env.example` is the public template | `.env`, `.env.*` |
| **Test runs** | never versioned (may contain real model output) | `test_runs/`, `test_runs_*/` |
| **Dialogue logs / private notes** | never versioned | `dialogue_logs/`, `private_notes/`, `local_configs/`, `memory/`, `runtime_state/` |

## Rules

1. **Source code is shareable.** Anything inside `core/`, `apps/`,
   `tools/`, `tests/`, `docs/architecture/` is engine, not data.
2. **Runtime data stays local.** Every file under `data/` *except*
   `data/persona.md` is gitignored. `data/persona.md` is intentionally
   tracked **in the dev branch** because it carries the working
   character biography; it is **excluded from `public/showcase`** via
   `git rm --cached`.
3. **Private persona module stays local in spirit.** The real
   `core/personas/maria.py` lives in the dev branch. The public branch
   removes it from tracking and ships only the sanitized
   `core/personas/maria.py.example` as a template.
4. **Secrets stay local.** Never commit `.env`. Use `.env.example` as
   the public-facing template. The runtime reads model names / Ollama
   endpoints / voice flags from env when present (see `config.py`).
5. **Dialogue logs / memory / opinion state never leave the machine.**
   These accumulate emotional and personal context; treat them like
   private notes.
6. **Test runs may leak.** `test_runs/` contains real model replies
   generated against your local persona; keep it ignored.

## How the two branches differ

| Topic | `direction/new-horizon-0.20.4.x` (dev) | `public/showcase` |
|---|---|---|
| `data/persona.md` | tracked | untracked |
| `core/personas/maria.py` | tracked | untracked |
| `core/personas/maria.py.example` | tracked (template; alongside real persona) | tracked (only template ships) |
| `LICENSE` | tracked | tracked |
| `.env.example` | tracked | tracked (under `examples/`) |
| `config.py` defaults | dev-grade (e.g. cloud profile) but env-driven | local-friendly defaults (`gemma3:4b`) |
| README tone | development / engineering log | sanitized public showcase overview |
| Tests with personal name | unchanged | personal name replaced with neutral `user` |

## How to add a new private artifact

1. Put it under one of: `data/`, `memory/`, `dialogue_logs/`,
   `runtime_state/`, `private_notes/`, `local_configs/`. These are
   already ignored.
2. If for some reason it must live elsewhere, add a precise rule to
   `.gitignore` *before* the file appears in `git status`.
3. Run `git status` — if it shows up as untracked, you're safe.
4. Never `git add -A` blindly. Add files by name.

## How to refresh the public branch

The public branch is meant to be rebuilt occasionally from the dev
branch by selective transfer (this very document is one of the things
that gets carried over). Workflow:

```bash
git checkout public/showcase
git checkout direction/new-horizon-0.20.4.x -- core/  apps/  tests/  docs/
# then re-apply public sanitization:
git rm --cached data/persona.md core/personas/maria.py 2>/dev/null || true
# replace personal identifiers in test asserts if any new ones appeared,
# review with: git grep -i "рустам"
git commit -am "Sync public showcase with dev"
```

`public/showcase` is **not** automatically merged into dev. Useful
infrastructure (LICENSE, `.env.example`, `maria.py.example`, this
document) flows the *other* way via cherry-pick or selective checkout.

## Pre-push checklist

Before pushing **anything** to a public remote of either branch:

```bash
git status
git ls-files | grep -Ei "(\.env$|^logs/|^memory/|dialogue_logs|runtime_state|unified_memory|vector_memory|opinions\.json|\.db$|\.sqlite)"
# Dev branch: data/persona.md IS expected. Public branch: it is NOT.
git ls-files | grep -E "data/persona\.md|core/personas/maria\.py$"
```

If the checks return private files on a branch where they should not
be — stop, do not push.
