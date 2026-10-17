# 05-tone-consistency Iteration Log (V2)

Evidence of prompt evolution following Red/Green/Refactor (EDD) discipline.

---

## Iteration 7 — Tables in Scope for Consultative Review

**Date:** 2026-10-21
**Problem:** A re-review of the 2026-Q3-IRONTRAX run found that step 05 had not reviewed table content; the G5 checker initially excluded table text. The result was 16 verbless or semicolon-joined evidence-gap fragments in table cells.
**Change:** Rewrote the skill to treat tables as part of “the document.” Added Section C (Table review): mandatory inventory of every table, row-level review, cell-type rules (evidence-label/reference shorthand preserved; narrative cells rewritten as complete sentences), frozen table structure, table–prose consistency check with `observations`, and row-level logging in `tone-changes.json`. Added `tables_reviewed` and `observations` top-level arrays to the output. Clarified that the Security Awareness exemption applies to awareness table cells. Added `table_interpretation` `style_marker`.
**Design decisions:**
- Table factual tokens remain under the substance freeze.
- Draft errors and mismatches are reported, not silently fixed.
- Optional placeholders never trigger a punch-out.
**Eval status:** Covered by existing E6/E7 and new E8 table-rewrite case (to be added). Attempted 2026-10-21; blocked because `OPENAI_API_KEY` and `ANTHROPIC_API_KEY` are not set.

## Iteration 6 — Client-Consultative Writing Standard

**Date:** 2026-10-21
**Problem:** Copilot feedback indicated the workflow rewarded assurance-style rigor (qualification, completeness) but lacked an equally strong definition of "consultative" client communication. Output risked reading like audit/Secure Score commentary rather than consultant guidance.
**Change:** Added a **Client-Consultative Writing Standard** to instruction 6: assessment-first ordering, consultant voice, plain language, qualification discipline, narrative-vs-evidence separation, client usefulness, and substance boundary. Reconciled existing bullets: shifted "verbose explanatory prose" to "concise explanatory prose," grounded the "so what" requirement in evidence, clarified that the seven elements are section-level checks (not a mandatory sentence sequence), and limited hedging to material qualifications.
**Design decisions:**
- Tables carry detailed evidence and framework mappings; prose explains posture, significance, priorities, and next steps.
- Short declarative sentences are preferred when they preserve the same meaning.
- One clear limitation is preferable to several defensive caveats.
- Plain language must never become unsupported certainty.
**Eval status:** Added E7 to `evals/promptfooconfig.yaml` to test consultative rewrite of caveat-heavy audit prose. Attempted 2026-10-21; blocked because `OPENAI_API_KEY` and `ANTHROPIC_API_KEY` are not set. Run `promptfoo eval` in `evals/` once keys are available.

## Iteration 5 — Sentence Discipline and Active Framing

**Date:** 2026-10-21
**Problem:** Reviewed output contained terse, fragment-heavy lines such as "X: It was not available" and multiple unrelated claims joined by semicolons.
**Change:** Added a **Sentence discipline and active framing** bullet to instruction 6: each sentence states one idea; semicolons connect only closely related clauses; evidence gaps are integrated as supporting context rather than left as raw fragments; scope is clarified in plain language; every substantive finding or risk is paired with a concrete client action; conclusions are framed as client posture or recommended action rather than passive descriptions of product flags.
**Design decisions:**
- Added `style_marker` values: `sentence_discipline`, `complete_sentences`, `scope_clarity`, `evidence_integration`, `active_framing`, `actionable_recommendations`.
- Mirrored checks in 06 G5 and the orchestrator.
**Eval status:** Covered by E2, E6, and E7. Attempted 2026-10-21; blocked because `OPENAI_API_KEY` and `ANTHROPIC_API_KEY` are not set. Run `promptfoo eval` in `evals/` once keys are available.

## Iteration 4 — Language Correctness and Sensitivity

**Date:** 2026-10-21
**Problem:** Skill had no explicit controls for spelling, grammar, punctuation, inclusive language, neutral perspective, or sensitive geopolitical references.
**Change:** Added a **Language correctness and sensitivity** bullet to instruction 6 covering spelling/grammar/punctuation conventions, consistent US English spelling, inclusive language, neutral third-person assessor perspective, and avoidance of sensitive geopolitical references.
**Design decisions:**
- Added `style_marker` values: `spelling`, `grammar`, `punctuation`, `inclusive_language`, `neutral_perspective`, `geopolitical_sensitivity`.
- Mirrored checks in 06 G5, the orchestrator, and `WORKFLOW.md`.
**Eval status:** Covered by E2 and E6. Attempted 2026-10-21; blocked because `OPENAI_API_KEY` and `ANTHROPIC_API_KEY` are not set. Run `promptfoo eval` in `evals/` once keys are available.

## Iteration 3 — Security Awareness Exemption

**Date:** 2026-10-21
**Problem:** A pilot run produced a punch-out for "introducing unsupported claims or invented evidence" in the Security Awareness section, which lacked comparable historical evidence.
**Change:** Added a **Security Awareness exemption** to instruction 3: sections whose heading matches "Security Awareness" (case-insensitive) are excluded from the conclusion-first analytical rewrite. Their existing content and facts are preserved unchanged; only template formatting alignment and banned-pattern cleanup are applied.
**Design decisions:**
- Prevents fabricated quarter movement or significance for this domain while keeping it in the final report.
**Eval status:** Covered by implicit template-conformance and Security Awareness handling. Attempted 2026-10-21; blocked because `OPENAI_API_KEY` and `ANTHROPIC_API_KEY` are not set. Run `promptfoo eval` in `evals/` once keys are available.

## Iteration 2 — Assessment-Synthesis Overhaul

**Date:** 2026-10-15
**Problem:** Initial V2 was still operating as an editorial rewrite. User required the skill to become a security-assessment synthesis skill with a substance freeze.
**Change:** Added the seven-element assessment-narrative checklist (condition, quarter movement, evidence, significance, qualification, recommended response, calibration), a quarterly-review requirement, cross-signal synthesis, and an extended `tone-changes.json` contract with `reason`, `assessment_function`, and `facts_changed`. Updated 06 G5 to check assessment quality and analytical coverage rather than style-only editorial quality.
**Design decisions:**
- Prior-quarter reports remain removed; quarterly comparison uses only evidence already in `draft.md`.
- `facts_changed` must be `false`; any `true` is a G5 hard fail.
- Added `style_marker` values: `client_significance`, `temporal_precision`, `significance_calibration`, `plain_voice`.
**Eval status:** E6 updated to include a `quarter_movement` record so the success case passes the coverage check. Attempted 2026-10-21; blocked because `OPENAI_API_KEY` and `ANTHROPIC_API_KEY` are not set. Run `promptfoo eval` in `evals/` once keys are available.

## Iteration 1 — Pivot to Assessment Synthesis

**Date:** 2026-10-14
**Change:** V2 fork. Removed the two prior-quarter reports as inputs; the editor now applies professional security-assessment writing standards directly instead of matching prior-report style. Inputs reduced to `draft.md` + `template`.
**Design decisions:**
- Role reframed as a professional editor applying an explicit standards checklist: third-person formal consultative voice, consistent tense, precise terminology, declarative cadence, hedged/evidence-attributed claims, findings as condition → evidence → implication, recommendations as action → rationale, banned patterns (first-person, contractions, colloquialisms, filler, marketing language).
- Template remains authoritative for structure/formatting only.
- Kept the substance freeze, per-edit `tone-changes.json` log, G4 input gate, missing-content punch-out, and audit record.
**Eval status:** Attempted 2026-10-21; blocked because `OPENAI_API_KEY` and `ANTHROPIC_API_KEY` are not set in the environment. Once keys are configured, run `promptfoo eval` in `evals/` and paste the summary into this log.
