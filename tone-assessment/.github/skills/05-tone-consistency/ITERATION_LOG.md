# 05-tone-consistency Iteration Log

Evidence of prompt evolution following Red/Green/Refactor (EDD) discipline.

---

## Iteration 1 — Redesign for 4-document input model

**Date:** 2026-10-14
**Change:** Rebuilt the skill around a new input contract: exactly ONE document (`draft.md`) is toned, compared against THREE read-only references — an authoritative template plus the two prior quarters' reports. Removed all upstream-pipeline inputs (`review.json`, `draft-map.json`, `style-profile.json`).

**Design decisions:**
- G4 redefined as an input-completeness gate (all 4 files present, hashed, template structure extractable).
- Style is derived per-run: structure/formatting from the template; voice/tense/phrasing from the prior reports.
- Explicit conflict-resolution rule: template wins over prior reports; newer report wins between the two reports.
- New punch-out A: template-required section missing from draft → halt for human, never fabricate.
- Kept the hard fact freeze, per-edit `tone-changes.json` log, and audit record.

**Eval status:** pending first `promptfoo eval` run against the six eval cases (E1–E6) in `evals/promptfooconfig.yaml`.
