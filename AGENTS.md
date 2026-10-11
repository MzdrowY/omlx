# oMLX-PL — polska wersja oMLX

Fork `jundot/omlx` (LLM inference server na Apple Silicon) z dodanym językiem polskim.
Upstream: https://github.com/jundot/omlx — licencja **Apache 2.0** (zachować nagłówki SPDX i `LICENSE`).

## Cel

Dodać `pl` do wszystkich trzech powierzchni językowych i wysłać PR do upstream:

1. `README.pl.md` — tłumaczenie README (wzór: `README.fr.md`, `README.zh.md`)
2. Panel admin — `omlx/admin/i18n/pl.json` (2019 kluczy, baza: `en.json`) + dopisanie opcji `pl` w `omlx/admin/templates/dashboard/_settings.html` (lista ~linii 71-80)
3. Aplikacja macOS — lokalizacja `pl` w `apps/omlx-mac/Resources/Localizable.xcstrings` (1274 kluczy, String Catalog, źródło `en`)

## Zasady tłumaczenia

- **Kluczy nie ruszamy** — tłumaczymy tylko wartości.
- Nie tłumaczyć: nazw własnych (oMLX, MLX, Hugging Face, Apple), nazw modeli (`Qwen3`, `gpt-oss-120b`), komend, ścieżek, kluczy API, kodu w backtickach.
- Zachować wszystkie placeholders (`{x}`, `%(...)s`, `%d`, `<tags>`, `{file:...}`) dokładnie 1:1.
- Ton: neutralny, jak w oryginale EN. Forma grzecznościowa: bezosobowa / druga osoba jak w EN (nie dodawać Pan/Pani).
- Angielskie terminy techniczne bez dobrej polskiej wersji zostają po angielsku (np. `prefix cache`, `batching`, `embedding`, `reranker`).
- Spójność z istniejącymi tłumaczeniami FR/ZH/KO/JA — sprawdzać jak one oddały dany termin.

## Workflow upstream (CONTRIBUTING.md)

- Jeden PR = jedna spójna zmiana (np. sam `pl.json` + wpis w `_settings.html`).
- Po zmianie kluczy web: zaktualizować `omlx/admin/i18n/en.json` i uruchomić `python scripts/normalize_i18n.py`.
- Po edycji szablonów/JS admina: `python omlx/admin/build_css.py`.
- Testy: `python -m pytest` (bez slow/integration). Zmiany UI: screenshot + sprawdzenie na żywym ekranie.
- PR opisuje: problem, podejście, co przetestowano. Maintainer robi squash-merge.

## Weryfikacja polskiej wersji

1. `python -c "import json; json.load(open('omlx/admin/i18n/pl.json'))"` — poprawny JSON.
2. Brak zgubionych placeholderów: skrypt porównujący `{...}` w en vs pl.
3. Start serwera z `ui.language = "pl"` — panel `/admin` po polsku, brak kluczy wypadających do EN (fallback jest dopuszczalny tylko dla nowych kluczy upstream).
4. `pytest` przechodzi.

---

# CLAUDE.md

Behavioral guidelines to reduce common LLM coding mistakes. Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

---

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, and clarifying questions come before implementation rather than after mistakes.
