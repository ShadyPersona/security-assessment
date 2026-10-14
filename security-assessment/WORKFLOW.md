# Workflow Definition — Quarterly Security Assessment Update

Converts the monolithic "update quarterly security assessment" skill into a
sequential multi-skill pipeline with one fail-closed deterministic guardrail on
every handoff, human punch-out gates, and a structured audit trail.

## Pipeline

```
[inputs] → 01-ingest ──G1──▶ 02-findings-matrix ──G2──▶ 03-draft-update ──G3──▶ 04-adversarial-review ──G4──▶ 05-tone-consistency ──G5──▶ 06-finalize → [deliverable]
```

## Inputs

| # | Source | Placeholder |
|---|--------|-------------|
| 1 | Previous quarter's report (baseline) | `<PREVIOUS QUARTER'S REPORT>` |
| 2 | Report from two quarters ago (history) | `<2 QUARTERS AGO REPORT>` |
| 3 | Current-quarter Defender export folder | `<DEFENDER EXPORT FOLDER LOCATION>` |
| 4 | CIS Microsoft 365 Foundations Benchmark PDF | `<CIS_Microsoft_365_Foundations_Benchmark PDF LOCATION>` |
| 5 | NIST CSF 2.0 reference PDF (NIST.CSWP.29) | `<NIST.CSWP.29 PDF LOCATION>` |

## Step Contract

Each step MUST run its inbound guardrail check and append the result to
`audit/guardrails.jsonl` before doing any work. A failed gate halts the
workflow.

## Run Layout

- Working artifacts live in `runs/<YYYY-QN>/` (see `runs/README.md`).
- Each run begins by copying `templates/run-state.json` into the run dir; it
  tracks `loop_counters` (G4/G5, max 2) and punch-out `approved` status.
- Every JSON artifact MUST validate against `schemas/<name>.schema.json` —
  schema validation is what makes the guardrails deterministic.
- Cross-run evidence lives in `audit/` (punch-outs, guardrails.jsonl,
  success-log.md, run-*.jsonl).

| Step | Skill | Consumes | Produces |
|------|-------|----------|----------|
| 01 | ingest | All 5 input sources | `inventory.json`, `evidence-index.json`, `style-profile.json` |
| 02 | findings-matrix | ingest outputs + reports + exports | `matrix.json` |
| 03 | draft-update | `matrix.json`, prior report, exports | `draft.md`, `draft-map.json` |
| 04 | adversarial-review | `draft.md`, `draft-map.json`, `matrix.json`, exports | `review.json`, `reviewed.md` |
| 05 | tone-consistency | `reviewed.md`, `style-profile.json`, prior report | `toned.md`, `tone-changes.json` |
| 06 | finalize | `toned.md`, all audit artifacts | Final report, `audit-report.md` |

## Guardrails

### G1 — ingest → matrix (Input Completeness Gate, hard fail)
- All 5 input sources resolved, SHA-256 hash recorded in `inventory.json`;
  Defender export folder contains ≥1 file.
- `evidence-index.json` maps every Defender export file to a security domain —
  an unmapped export fails the gate.
- Prior report's section map and `style-profile.json` were extracted.
- Not waivable: missing input halts the workflow.

### G2 — matrix → draft (Evidence Citation Gate + Punch-outs A & C)
- Schema validation of `matrix.json`: every row has an evidence file path OR
  classification `unable-to-validate`; required fields present (section,
  finding, prior state, evidence, classification, CIS ref, NIST ref).
- **Punch-out A:** any `unable-to-validate` rows → write
  `audit/punchout-unable-to-validate.md` → halt for human sign-off.
- **Punch-out C:** any `regressed` rows → write
  `audit/punchout-regressions.md` → halt for human confirmation.
- Drafting is blocked until every generated punch-out file is signed off.

### G3 — draft → review (Structural & Framework Reference Gate + Punch-outs B & D)
- CIS/NIST references extracted from `draft.md` vs prior report and diffed;
  changed/added mappings → **Punch-out B** (`audit/punchout-framework-mappings.md`).
- Structural diff of `draft.md` headings vs prior report; added/removed
  sections → **Punch-out D** (`audit/punchout-structure-changes.md`).
- `draft-map.json` must reference ≥1 matrix row per section — orphan prose is
  a hard fail.

### G4 — review → tone (Zero Unresolved Claims Gate)
- Every claim in `review.json` has verdict `confirmed` or `fixed`; any
  `unresolved`/`rejected`/missing verdict blocks the handoff.
- Every `fixed` claim carries a diff entry showing the applied change.
- Coverage check: claims reviewed ≥ matrix row IDs cited in `draft-map.json`.
- Unresolved claims loop back to step 03 (max 2 iterations → human escalation
  logged in `audit/punchout-log.md`).

### G5 — tone → finalize (Tone & Fact-Preservation Gate)
- Tone check: `toned.md` scored against `style-profile.json` — voice/tense
  conformance, banned patterns (first-person, unhedged claims, internal
  classification labels appearing as literal report text), required phrasing
  conventions from the prior report.
- Fact-preservation diff: every number, date, percentage, CIS/NIST reference,
  and finding title in `toned.md` must exist in `reviewed.md` — the tone pass
  cannot invent or alter data.
- Failure → loop back to step 05 (max 2 iterations → human escalation).

## Punch-Out Points

| ID | Trigger | Raised by | File | Human decision |
|----|---------|-----------|------|----------------|
| A | `unable-to-validate` matrix rows | G2 | `audit/punchout-unable-to-validate.md` | mark each `still-present` / `removed` / `defer` |
| B | CIS/NIST reference changes | G3 | `audit/punchout-framework-mappings.md` | approve each changed mapping |
| C | `regressed` matrix rows | G2 | `audit/punchout-regressions.md` | confirm evidence supports each regression |
| D | added/removed report sections | G3 | `audit/punchout-structure-changes.md` | approve/reject each structural change |
| E | G4/G5 loop exhaustion | G4/G5 | `audit/punchout-log.md` | human resolves stalled claims/tone directly |

Every halt, bypass test, and human decision is recorded in
`audit/punchout-log.md`.

## Evidence Rules (carried from the source skill)

1. Current-quarter Defender exports are authoritative for CURRENT STATE.
2. Previous quarter's report is authoritative for what was previously reported.
3. Two-quarters-ago report is for historical context and update methodology only.
4. CIS/NIST PDFs are authoritative for framework references and terminology.
5. Never assume a previously documented condition still exists; never assume a
   recommendation was implemented without current-quarter evidence.
6. Distinguish: `confirmed by current data` / `not observed in current data` /
   `unable to validate from the provided data`.
7. No fabricated findings, metrics, configurations, dates, scores,
   percentages, framework mappings, or recommendations.

## Audit Trail

`audit/run-YYYYMMDD.jsonl` — one record per step:

```json
{"step": "03-draft-update", "model": "...", "input_hashes": {...}, "output_hashes": {...},
 "tokens_in": 0, "tokens_out": 0, "cost_usd": 0.0, "finding_ids": ["F-001"],
 "guardrail_inbound": {"id": "G2", "result": "pass"}}
```

Any failure must be traceable to its exact origin step.

## End-to-End Success Metric

A run succeeds iff:
1. G5 passes with zero unresolved claims,
2. every triggered punch-out is resolved with a recorded human decision, and
3. the final report is emitted.

Per-run result appended to `audit/success-log.md`:

```
| date | run id | result | punch-outs | iterations | notes |
```

Success rate = successful runs / total runs, tracked quarter over quarter.
