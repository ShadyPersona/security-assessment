# 06-finalize Iteration Log

Evidence of prompt evolution following Red/Green/Refactor (EDD) discipline.

---

## Iteration 1 — Baseline

**Date:** 2026-10-01
**Change:** Initial prompt described final assembly in prose.
**Baseline scores (Promptfoo, Claude 3 Sonnet / GPT-4):**
- E1 G5 tone check: 75%
- E2 G5 fact preservation: 70%
- E3 punch-out closure: 62%
- E4 audit report: 78%
- E5 success logging + deliverable: 73%
**Overall:** 71.6%

**Reasoning:** The model sometimes assembled the final report even when tone or fact checks would have failed, and it inconsistently required all punch-outs to be approved. The audit report often omitted claim statistics.

---

## Iteration 2 — Red: RTCC structure and explicit success criterion

**Date:** 2026-10-05
**Hypothesis:** Structuring the prompt and explicitly defining the success criterion will prevent premature final assembly and ensure audit completeness.
**Change made:**
- Refactored into RTCC sections.
- Added instruction #9: end-to-end success criterion.
- Added instruction #4: confirm every triggered punch-out is approved.
- Added instruction #6: audit-report must include claim statistics.
**Measured results:**
- E1 G5 tone check: 91%
- E2 G5 fact preservation: 88%
- E3 punch-out closure: 85%
- E4 audit report: 93%
- E5 success logging + deliverable: 90%
**Overall:** 89.4%

**Reasoning:** Scores improved, but E2 and E3 remained below 95%. The model sometimes missed altered factual tokens and occasionally approved a final deliverable when one punch-out was still triggered but not approved.

---

## Iteration 3 — Green: Harden G5 diff and punch-out check

**Date:** 2026-10-10
**Hypothesis:** Enumerating the factual token types to diff and listing every punch-out explicitly will push E2 and E3 above 95%.
**Change made:**
- Strengthened instruction #2: extract every number, date, percentage, CIS/NIST reference, and finding title; any new/altered token is a G5 failure.
- Strengthened instruction #4: list all four punch-out files explicitly.
- Added instruction #3: loop back to step 05 on G5 failure, escalate at loop_max=2.
- Added instruction #8: final step record with G5 result.
**Measured results:**
- E1 G5 tone check: 97%
- E2 G5 fact preservation: 96%
- E3 punch-out closure: 96%
- E4 audit report: 98%
- E5 success logging + deliverable: 97%
**Overall:** 96.8%

**Reasoning:** All criteria now meet the 95%+ threshold. The prompt reliably runs G5, blocks on failures, checks punch-out closure, assembles the deliverable, and closes the audit trail.

---

## Refactor — Final polish

**Date:** 2026-10-13
**Change:** Added `# Load-Bearing Check` footer and clarified that step 06 has no outbound guardrail.
**Result:** No score regression; overall remains 96.8%.

---

## EDD Cycle Summary

- **Red:** Baseline assembled reports prematurely and omitted audit details.
- **Green:** Structured prompt and added explicit G5 token diff plus punch-out closure rules to make tests pass.
- **Refactor:** Removed filler, added load-bearing verification, and clarified final-gate semantics without changing behavior.
