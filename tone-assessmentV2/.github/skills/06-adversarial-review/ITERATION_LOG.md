# 06-adversarial-review Iteration Log (V2)

Evidence of prompt evolution following Red/Green/Refactor (EDD) discipline.

---

## Iteration 4 — G5 Expanded to Review Table Cells

**Date:** 2026-10-21
**Problem:** Step 05 was updated to review tables, but G5 did not yet explicitly scan table cells or verify table edits in `tone-changes.json`.
**Change:** Updated G5 assessment-quality check to scan table captions, row labels, and narrative cells for banned patterns, vague temporal terms, and sentence discipline. Updated substance-freeze check to cover factual tokens inside tables. Updated change-accountability check to require `tables_reviewed` and `observations` arrays and to verify table edits row by row. A draft table missing from `tables_reviewed` is now a G5 failure.
**Design decisions:**
- Keeps 06 as the adversarial reviewer that independently challenges 05's table handling.
- Factual tokens inside tables remain protected by the substance freeze.
**Eval status:** Covered by existing E1–E7 and new E8 table-substance-freeze case (to be added). Attempted 2026-10-21; blocked because `OPENAI_API_KEY` and `ANTHROPIC_API_KEY` are not set.

## Iteration 3 — G5 Client-Consultative Quality Check

**Date:** 2026-10-21
**Problem:** The client-consultative writing standard was added to step 05, but G5 did not yet explicitly evaluate whether the output read as consultant guidance rather than audit/Secure Score commentary.
**Change:** Updated the G5 assessment-quality check in `SKILL.md` to include a **client-consultative quality** clause: sections lead with supported conclusions, technical findings are interpreted for the client, Microsoft/CIS/NIST inputs are supporting rather than narrative voice, prose is concise consultative without defensive caveat-stacking, and every substantive domain answers what was assessed, what changed, why it matters, and what's next.
**Design decisions:**
- Preserved all existing G5 rigor (assessment depth, substance freeze, change accountability, punch-out closure).
- Added the new checks as an explicit subsection so the adversarial reviewer can cite them.
**Eval status:** Added E7 to `evals/promptfooconfig.yaml` to verify G5 failure blocks final deliverable assembly. Attempted 2026-10-21; blocked because `OPENAI_API_KEY` and `ANTHROPIC_API_KEY` are not set. Run `promptfoo eval` in `evals/` once keys are available.

## Iteration 2 — Refactor 06 as Adversarial Review & Finalize

**Date:** 2026-10-21
**Problem:** Stage 4 certification expects adversarial review agents that independently challenge prior-step outputs. `06-finalize` was performing adversarial review implicitly through its inbound G5 checks, but the skill name and role did not make that explicit.
**Change:** Renamed skill to `06-adversarial-review`. Rewrote role to "Adversarial Review & Finalize agent." Split instructions into Phase 1 (G5 adversarial review) and Phase 2 (finalization on pass). Updated success criterion and output spec to require logging on both pass and fail. Updated orchestrator, `WORKFLOW.md`, `README.md`, `templates/run-state.json`, and evals.
**Design decisions:**
- Finalization is blocked until G5 passes and all triggered punch-outs are approved.
- Failure logs must still be written so the end-to-end success rate has an accurate denominator.
**Eval status:** Added E7 to `evals/promptfooconfig.yaml`. Attempted 2026-10-21; blocked because `OPENAI_API_KEY` and `ANTHROPIC_API_KEY` are not set. Run `promptfoo eval` in `evals/` once keys are available.

## Iteration 1 — Align G5 with the Assessment-Synthesis Model

**Date:** 2026-10-14
**Change:** V2 fork. G5 assessment-quality check now scores `toned.md` against the professional and analytical standards defined in step 05 (conclusion-first elements, quarterly thesis, cross-signal synthesis, voice, tense, terminology, cadence, hedging, explanatory structures, banned patterns, language correctness and sensitivity) instead of prior-report conventions. Assessment-depth requirement kept: an unchanged or word-patched `toned.md` fails even with zero banned patterns. Inputs reduced to `toned.md`, `draft.md`, `template`, `tone-changes.json`, `run-state.json`, audit artifacts.
**Eval status:** Attempted 2026-10-21; blocked because `OPENAI_API_KEY` and `ANTHROPIC_API_KEY` are not set. Once keys are configured, run `promptfoo eval` in `evals/` and paste the summary into this log.
