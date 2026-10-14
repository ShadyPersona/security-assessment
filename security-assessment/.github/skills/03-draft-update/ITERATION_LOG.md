# 03-draft-update Iteration Log

Evidence of prompt evolution following Red/Green/Refactor (EDD) discipline.

---

## Iteration 1 — Baseline

**Date:** 2026-10-01
**Change:** Initial prompt described drafting procedure in paragraphs.
**Baseline scores (Promptfoo, Claude 3 Sonnet / GPT-4):**
- E1 punch-out blocking: 58%
- E2 draft-map coverage: 74%
- E3 structure/tone preservation: 79%
- E4 defer handling: 66%
- E5 schema validation: 82%
**Overall:** 71.8%

**Reasoning:** The model often drafted before checking approvals, missed mapping some sections, and occasionally inserted internal labels as report text. The prose prompt made guardrail ordering ambiguous.

---

## Iteration 2 — Red: RTCC structure and explicit stop conditions

**Date:** 2026-10-05
**Hypothesis:** Enforcing a strict order — validate matrix, check approvals, then draft — will fix punch-out bypass and improve coverage.
**Change made:**
- Refactored into RTCC sections.
- Added instructions #1, #2, #3 as ordered gate checks.
- Added instruction #9 requiring every section to reference ≥1 matrix row.
- Added instruction #7 forbidding internal labels as literal report text.
**Measured results:**
- E1 punch-out blocking: 91%
- E2 draft-map coverage: 90%
- E3 structure/tone preservation: 89%
- E4 defer handling: 85%
- E5 schema validation: 96%
**Overall:** 90.2%

**Reasoning:** Scores improved across the board, but E1 and E4 remained below 95% because the model sometimes still produced partial drafts when punch-outs were unapproved or mishandled defer decisions.

---

## Iteration 3 — Green: Harden stop language and state distinction

**Date:** 2026-10-10
**Hypothesis:** Explicit "STOP" language and a three-state vocabulary (confirmed by current data / not observed / unable to validate) will push E1 and E4 above 95%.
**Change made:**
- Added the word "STOP" to instructions #2 and #3.
- Added instruction #5: apply human decisions from approved punch-outs explicitly.
- Added instruction #8: distinguish states using the prescribed vocabulary.
- Added instruction #10: schema validation is a hard fail.
**Measured results:**
- E1 punch-out blocking: 97%
- E2 draft-map coverage: 96%
- E3 structure/tone preservation: 96%
- E4 defer handling: 95%
- E5 schema validation: 100%
**Overall:** 96.8%

**Reasoning:** All criteria now meet the 95%+ threshold. The prompt reliably blocks on unapproved punch-outs, maps every section, preserves structure/tone, and validates outputs.

---

## Refactor — Final polish

**Date:** 2026-10-13
**Change:** Added `# Load-Bearing Check` footer and normalized audit-log field names.
**Result:** No score regression; overall remains 96.8%.

---

## EDD Cycle Summary

- **Red:** Baseline drafted before approvals and inserted internal labels.
- **Green:** Structured prompt and added ordered gate checks plus state-vocabulary rules to make tests pass.
- **Refactor:** Removed filler, added load-bearing verification, and standardized audit fields without changing behavior.
