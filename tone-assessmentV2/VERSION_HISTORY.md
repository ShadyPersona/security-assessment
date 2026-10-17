# Version History — tone-assessmentV2

This document records the design lineage of the tone-assessment workflow for Stage 4 certification. It maps major versions to Git commits, iteration logs, and eval coverage.

---

## V1 — tone-assessment/

**Commit range:** `27fd016` → `5096a1e` → `62c76b5`

**Design:**
- Two-step workflow: `05-tone-consistency` → `06-finalize`.
- Four inputs: `draft.md`, `template`, `report-prev-quarter`, `report-2-quarters-ago`.
- Step 05 derived the target voice by reading two prior-quarter reports and matching their style.
- Guardrails: G4 input completeness, G5 tone/structure/fact check.
- Punch-outs: missing content (A) and loop exhaustion (E).

**Why archived:** The prior-report input model conflicted with the goal of producing a self-contained assessment. The agent sometimes retrieved the V1 skill while the V2 README governed, causing ambiguous instructions.

**Artifacts:** Archived in Git history; last V1 commit is `62c76b5`.

---

## V2 — tone-assessmentV2/

**Commit:** `bb8f307` (replace V1 with V2) → `2ed166d` (refactor 06 into adversarial review)

**Design:**
- Two-step workflow: `05-tone-consistency` → `06-adversarial-review`.
- Two inputs: `draft.md` (factual source) and `template` (structural authority).
- Step 05 is an **assessment synthesizer**: it converts telemetry/control data into conclusion-led, quarter-over-quarter security analysis while freezing every fact.
- Step 06 is an **adversarial reviewer + finalizer**: it independently challenges 05’s output via G5, then assembles the deliverable only on pass.
- Guardrails:
  - G4: input-completeness gate (before 05).
  - G5: assessment quality, substance freeze, change accountability, punch-out closure (between 05 and 06).
- Punch-outs:
  - A: missing content when the template requires a section the draft lacks.
  - E: loop exhaustion when G4 or G5 fails twice.

### V2 major changes

| Change | Date | Where documented |
|---|---|---|
| V2 fork: drop prior-quarter reports; apply professional standards directly | 2026-10-14 | `05-tone-consistency/ITERATION_LOG.md` Iteration 1 |
| Assessment-synthesis overhaul: seven-element checklist, quarterly thesis, cross-signal synthesis, extended `tone-changes.json` | 2026-10-15 | `05-tone-consistency/ITERATION_LOG.md` Iteration 2 |
| Security Awareness exemption | 2026-10-21 | `05-tone-consistency/ITERATION_LOG.md` Iteration 3 |
| Language correctness and sensitivity standards | 2026-10-21 | `05-tone-consistency/ITERATION_LOG.md` Iteration 4 |
| Sentence discipline and active framing | 2026-10-21 | `05-tone-consistency/ITERATION_LOG.md` Iteration 5 |
| Client-consultative writing standard | 2026-10-21 | `05-tone-consistency/ITERATION_LOG.md` Iteration 6 |
| Align G5 with assessment-synthesis model | 2026-10-14 | `06-adversarial-review/ITERATION_LOG.md` Iteration 1 |
| Refactor 06 as Adversarial Review & Finalize | 2026-10-21 | `06-adversarial-review/ITERATION_LOG.md` Iteration 2 |
| G5 client-consultative quality check | 2026-10-21 | `06-adversarial-review/ITERATION_LOG.md` Iteration 3 |
| Tables in scope for consultative review | 2026-10-21 | `05-tone-consistency/ITERATION_LOG.md` Iteration 7 |
| G5 expanded to review table cells | 2026-10-21 | `06-adversarial-review/ITERATION_LOG.md` Iteration 4 |

### Eval coverage

| Skill | Eval file | Cases |
|---|---|---|
| 05-tone-consistency | `evals/promptfooconfig.yaml` | E1 G4 failure, E2 synthesis, E3 condition/evidence/implication, E4 substance freeze, E5 missing-content punch-out, E6 edit logging, E7 consultative rewrite |
| 06-adversarial-review | `evals/promptfooconfig.yaml` | E1 assessment-quality failure, E2 substance-freeze failure, E3 change-accountability failure, E4 unapproved punch-out, E5 no depth, E6 success, E7 G5 blocks finalization |

**Eval execution status:** Promptfoo is installed, but runs require `OPENAI_API_KEY` and `ANTHROPIC_API_KEY`. Once keys are set, run `promptfoo eval` in each skill's `evals/` directory and paste the pass/fail summary into the corresponding `ITERATION_LOG.md`.

---

## Git tags

- `tone-assessment-v1` → `62c76b5`
- `tone-assessment-v2` → current `HEAD`
