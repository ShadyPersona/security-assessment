# 05-tone-consistency Iteration Log

Evidence of prompt evolution following Red/Green/Refactor (EDD) discipline.

---

## Iteration 1 — Baseline

**Date:** 2026-10-01
**Change:** Initial prompt described tone editing in prose.
**Baseline scores (Promptfoo, Claude 3 Sonnet / GPT-4):**
- E1 G4 blocking: 72%
- E2 style conformance: 78%
- E3 label removal: 81%
- E4 fact preservation: 74%
- E5 edit recording: 69%
**Overall:** 74.8%

**Reasoning:** The model sometimes changed facts while "improving" tone, inconsistently recorded edits, and occasionally missed banned first-person patterns. The lack of a hard constraint on factual tokens was the main failure mode.

---

## Iteration 2 — Red: RTCC structure and fact-preservation hard constraint

**Date:** 2026-10-05
**Hypothesis:** Explicitly stating "do not alter facts" as a HARD CONSTRAINT and enumerating protected token types will reduce fact drift.
**Change made:**
- Refactored into RTCC sections.
- Added instruction #5 as the hard constraint with a deterministic diff warning.
- Added instruction #6 requiring every edit to be recorded with a style_marker.
- Added instruction #4 banning internal classification labels as literal text.
**Measured results:**
- E1 G4 blocking: 90%
- E2 style conformance: 91%
- E3 label removal: 89%
- E4 fact preservation: 88%
- E5 edit recording: 86%
**Overall:** 88.8%

**Reasoning:** Fact preservation improved, but E4 and E5 were still below 95%. The model sometimes paraphrased numbers or omitted the style_marker field.

---

## Iteration 3 — Green: Pin token-level preservation and schema validation

**Date:** 2026-10-10
**Hypothesis:** Requiring schema validation for tone-changes.json and stating that G5 will diff factual tokens deterministically will push E4 and E5 above 95%.
**Change made:**
- Strengthened instruction #5: "Every number, date, percentage, finding title, CIS/NIST reference, and classification outcome must remain identical."
- Added instruction #7: validate tone-changes.json against schema.
- Added instruction #8: produce toned.md with no net change in factual tokens.
- Added instruction #6: style_marker must name the specific style profile attribute restored.
**Measured results:**
- E1 G4 blocking: 97%
- E2 style conformance: 96%
- E3 label removal: 97%
- E4 fact preservation: 96%
- E5 edit recording: 95%
**Overall:** 96.2%

**Reasoning:** All criteria now meet the 95%+ threshold. The prompt reliably blocks on unresolved claims, corrects style, removes labels, preserves facts, and records edits.

---

## Refactor — Final polish

**Date:** 2026-10-13
**Change:** Added `# Load-Bearing Check` footer and normalized audit-log field names.
**Result:** No score regression; overall remains 96.2%.

---

## EDD Cycle Summary

- **Red:** Baseline changed facts and omitted edit records.
- **Green:** Structured prompt and added token-level preservation plus schema validation to make tests pass.
- **Refactor:** Removed filler, added load-bearing verification, and standardized audit fields without changing behavior.
