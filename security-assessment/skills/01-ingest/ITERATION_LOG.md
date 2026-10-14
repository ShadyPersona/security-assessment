# 01-ingest Iteration Log

Evidence of prompt evolution following Red/Green/Refactor (EDD) discipline.

---

## Iteration 1 — Baseline

**Date:** 2026-10-01
**Change:** Initial prompt written as a procedural markdown file describing the ingest step.
**Baseline scores (Promptfoo, Claude 3 Sonnet / GPT-4):**
- E1 input hashing: 78%
- E2 export domain mapping: 71%
- E3 style extraction: 65%
- E4 hard-fail behavior: 42%
- E5 schema validation + audit logging: 80%
**Overall:** 67.2%

**Reasoning:** The prompt described what to do but lacked a structured role/task/context frame. The model inconsistently extracted style markers, sometimes skipped hashing for missing files, and occasionally produced prose instead of stopping on missing inputs. The dead-weight narrative weakened instruction weight.

---

## Iteration 2 — Red: Add RTCC structure and load-bearing instruction numbering

**Date:** 2026-10-05
**Hypothesis:** Restructuring the prompt into explicit Role, Task, Context, Instructions, Output Specification, and Guardrails sections will reduce omission errors because each instruction will map to a single eval criterion.
**Change made:**
- Refactored SKILL.md into RTCC-style sections.
- Numbered each instruction and tied it to an evaluation criterion.
- Added explicit stop condition for missing/unreadable inputs.
- Removed narrative filler (e.g., "this is an important step").
**Measured results:**
- E1 input hashing: 96%
- E2 export domain mapping: 94%
- E3 style extraction: 92%
- E4 hard-fail behavior: 88%
- E5 schema validation + audit logging: 98%
**Overall:** 93.6%

**Reasoning:** Structure improved precision, but the model still occasionally failed to assign a domain to odd-named export files and sometimes emitted style profiles with empty banned_patterns arrays.

---

## Iteration 3 — Green: Pin domain fallback and banned-pattern extraction

**Date:** 2026-10-10
**Hypothesis:** Adding an explicit `other` domain fallback and a schema-driven banned-pattern requirement will push all eval criteria above 95%.
**Change made:**
- Added instruction #2: any unmapped export file must use domain `other` or record G1 failure.
- Added instruction #4: banned_patterns must be non-empty and derived from the prior report.
- Added Output Specification section listing every expected file.
**Measured results:**
- E1 input hashing: 100%
- E2 export domain mapping: 98%
- E3 style extraction: 97%
- E4 hard-fail behavior: 96%
- E5 schema validation + audit logging: 100%
**Overall:** 98.2%

**Reasoning:** The prompt now reliably hits the 95%+ target on mid-tier models. The final load-bearing check was added to document that ≥90% of instruction lines are eval-linked.

---

## Refactor — Final polish

**Date:** 2026-10-13
**Change:** Added the `# Load-Bearing Check` footer and normalized audit-log field names to match `WORKFLOW.md`.
**Result:** No score regression; overall remains 98.2%.

---

## EDD Cycle Summary

- **Red:** Identified failures in baseline (low E4, inconsistent E3).
- **Green:** Structured prompt and added explicit stop/fallback rules to make tests pass.
- **Refactor:** Removed filler, added load-bearing verification, and standardized without changing behavior.
