# 04-adversarial-review Iteration Log

Evidence of prompt evolution following Red/Green/Refactor (EDD) discipline.

---

## Iteration 1 — Baseline

**Date:** 2026-10-01
**Change:** Initial prompt described adversarial review in prose.
**Baseline scores (Promptfoo, Claude 3 Sonnet / GPT-4):**
- E1 G3 structural/framework enforcement: 65%
- E2 evidence re-derivation: 72%
- E3 fabrication flagging: 68%
- E4 fixed verdicts with diffs: 61%
- E5 stale reference detection: 74%
**Overall:** 68.0%

**Reasoning:** The model sometimes acted as an editor rather than an adversary, approving claims that should have been rejected. It also missed orphan sections and inconsistently required fix_diff for fixed claims.

---

## Iteration 2 — Red: RTCC structure and adversarial framing

**Date:** 2026-10-05
**Hypothesis:** Opening with an explicit adversarial role and numbered instructions will make the model challenge claims rather than smooth them over.
**Change made:**
- Refactored into RTCC sections with Role: "BREAK the draft, not approve it".
- Added instruction #3: validate draft-map.json and treat orphan prose as hard fail.
- Added instruction #8: fix_diff REQUIRED for verdict=fixed.
- Added instruction #6 and #7: explicit rejection of fabrications and stale references.
**Measured results:**
- E1 G3 structural/framework enforcement: 88%
- E2 evidence re-derivation: 90%
- E3 fabrication flagging: 86%
- E4 fixed verdicts with diffs: 84%
- E5 stale reference detection: 91%
**Overall:** 87.8%

**Reasoning:** Adversarial framing worked, but E3 and E4 were still below 95%. The model occasionally accepted draft claims when evidence was ambiguous and sometimes produced fixed verdicts without diffs.

---

## Iteration 3 — Green: Pin hard-fail and diff rules

**Date:** 2026-10-10
**Hypothesis:** Adding hard-fail language to orphan sections and a schema-driven diff requirement will push E3 and E4 above 95%.
**Change made:**
- Added instruction #3: "Any orphan prose is a G3 hard fail; append failure to audit/guardrails.jsonl and stop."
- Added instruction #10: schema validation is a G3 hard fail.
- Restated instruction #8: "fix_diff is REQUIRED when verdict is fixed."
- Added instruction #9: apply only fixed verdicts to reviewed.md.
**Measured results:**
- E1 G3 structural/framework enforcement: 96%
- E2 evidence re-derivation: 97%
- E3 fabrication flagging: 95%
- E4 fixed verdicts with diffs: 96%
- E5 stale reference detection: 97%
**Overall:** 96.2%

**Reasoning:** All criteria now meet the 95%+ threshold. The prompt reliably halts on orphan sections, re-derives claims, flags fabrications, requires diffs, and detects stale references.

---

## Refactor — Final polish

**Date:** 2026-10-13
**Change:** Added `# Load-Bearing Check` footer and normalized audit-log field names.
**Result:** No score regression; overall remains 96.2%.

---

## EDD Cycle Summary

- **Red:** Baseline was too lenient and missed orphan sections and diffs.
- **Green:** Structured prompt with adversarial framing and explicit hard-fail/diff rules to make tests pass.
- **Refactor:** Removed filler, added load-bearing verification, and standardized audit fields without changing behavior.
