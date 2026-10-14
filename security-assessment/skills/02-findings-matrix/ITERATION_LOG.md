# 02-findings-matrix Iteration Log

Evidence of prompt evolution following Red/Green/Refactor (EDD) discipline.

---

## Iteration 1 — Baseline

**Date:** 2026-10-01
**Change:** Initial prompt described the matrix-building procedure in narrative form.
**Baseline scores (Promptfoo, Claude 3 Sonnet / GPT-4):**
- E1 G1 enforcement: 70%
- E2 evidence-linked classification: 76%
- E3 anti-fabrication: 62%
- E4 punch-out behavior: 55%
- E5 schema validation: 81%
**Overall:** 68.8%

**Reasoning:** Without explicit numbered instructions tied to criteria, the model sometimes skipped G1, invented evidence paths, or approved its own punch-outs. The narrative style diluted the guardrail imperative.

---

## Iteration 2 — Red: RTCC structure and explicit evidence rule

**Date:** 2026-10-05
**Hypothesis:** Converting the prompt into Role/Task/Context/Instructions and adding a hard evidence rule (`evidence_path` OR `unable-to-validate`) will reduce fabricated rows and improve G1 reliability.
**Change made:**
- Refactored into RTCC sections.
- Added instruction #6 as the explicit evidence rule.
- Added instruction #8 and #9 to require punch-out file creation without self-approval.
- Removed background narrative about the importance of accuracy.
**Measured results:**
- E1 G1 enforcement: 94%
- E2 evidence-linked classification: 92%
- E3 anti-fabrication: 87%
- E4 punch-out behavior: 84%
- E5 schema validation: 97%
**Overall:** 90.8%

**Reasoning:** Most criteria improved, but E3 and E4 still fell short of 95%. The model occasionally inferred "regressed" from ambiguous evidence and failed to create punch-out files for `unable-to-validate` rows.

---

## Iteration 3 — Green: Pin classification rules and punch-out triggers

**Date:** 2026-10-10
**Hypothesis:** Adding explicit stop conditions for punch-outs and a classification menu will push E3 and E4 above 95%.
**Change made:**
- Added instruction #4: no evidence → `unable-to-validate`.
- Added instruction #8 and #9 with explicit `triggered: true` updates to `run-state.json`.
- Added instruction #5 requiring valid `F-[0-9]{3,}` ids.
- Added instruction #7: schema violation = G2 failure.
**Measured results:**
- E1 G1 enforcement: 98%
- E2 evidence-linked classification: 97%
- E3 anti-fabrication: 96%
- E4 punch-out behavior: 95%
- E5 schema validation: 100%
**Overall:** 97.2%

**Reasoning:** All criteria now meet the 95%+ threshold. The prompt reliably creates the matrix, links rows to evidence, raises punch-outs, and validates schema.

---

## Refactor — Final polish

**Date:** 2026-10-13
**Change:** Added the `# Load-Bearing Check` footer and standardized audit-log field names.
**Result:** No score regression; overall remains 97.2%.

---

## EDD Cycle Summary

- **Red:** Baseline had weak guardrail and fabrication issues.
- **Green:** Structured prompt and added explicit evidence/punch-out rules to make tests pass.
- **Refactor:** Removed filler, added load-bearing verification, and standardized audit fields without changing behavior.
