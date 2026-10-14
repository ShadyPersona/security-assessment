# Workflow Definition — Tone & Finalize Pipeline

A two-step sequential workflow that takes the reviewed security-assessment
draft through tone normalization and final delivery, with one fail-closed
deterministic guardrail on every handoff, human punch-out gates, and a
structured audit trail. Fully self-contained in this folder — no paths,
files, or skills outside `tone-assessment/` are referenced.

## Pipeline

```
[inputs] → 05-tone-consistency ──G5──▶ 06-finalize → [deliverable]
              ▲ G4 inbound
              │
   (halts if review.json has unresolved claims
    or unapproved upstream punch-outs block sign-off)
```

## Inputs

All inputs are placed in `runs/<YYYY-QN>/inputs/` before the run starts.

| # | Source | Produced by (upstream) | Consumed by |
|---|--------|------------------------|-------------|
| 1 | Previous quarter's report | client deliverable archive | 05 (style/format baseline), 06 (delivery format) |
| 2 | `reviewed.md` | upstream adversarial-review step | 05, 06 (fact-diff reference) |
| 3 | `review.json` | upstream adversarial-review step | 05 (G4 claims gate) |
| 4 | `draft-map.json` | upstream drafting step | 05 (G4 coverage check) |
| 5 | `style-profile.json` | upstream ingest step | 05 (tone target), 06 (G5 tone check) |
| 6 | `run-state.json` | copied from `templates/` at run start | 05, 06 |

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
| 05 | tone-consistency | `reviewed.md`, `review.json`, `draft-map.json`, `style-profile.json`, prior report | `toned.md`, `tone-changes.json` |
| 06 | finalize | `toned.md`, `reviewed.md`, `style-profile.json`, `run-state.json`, all audit artifacts | Final report, `audit-report.md` |

## Guardrails

Guardrails sit **between** workflow steps (and inbound on step 05) — they are
deterministic checks run before the next agent is allowed to proceed, not
review performed inside a step. All are automated; human review between
steps would not satisfy this design.

### G4 — review → tone (Zero Unresolved Claims Gate, enforced by step 05)
- `review.json` validates against `schemas/review.schema.json`; every claim
  has verdict `confirmed` or `fixed`; any `unresolved`/`rejected`/missing
  verdict blocks the handoff.
- Every `fixed` claim carries a `fix_diff` entry showing the applied change.
- Coverage check: claims reviewed ≥ matrix row IDs cited in `draft-map.json`
  (validated against `schemas/draft-map.schema.json`).
- Failure → increment `loop_counters.G4`, retry; at `loop_max` (2) escalate
  to human via punch-out E in `audit/punchout-log.md`.

### G5 — tone → finalize (Tone & Fact-Preservation Gate, enforced by step 06)
- Tone check: `toned.md` scored against `style-profile.json` — voice/tense
  conformance, banned patterns absent (first-person, unhedged claims,
  internal classification labels as literal report text), required phrasing
  conventions present.
- Fact-preservation diff: every number, date, percentage, CIS/NIST
  reference, and finding title in `toned.md` must exist in `reviewed.md` —
  the tone pass cannot invent or alter data.
- Punch-out closure: every punch-out in `run-state.json` with
  `triggered: true` must also have `approved: true`. An unapproved triggered
  punch-out is a G5 hard fail — the run STOPs for human sign-off.
- Failure → loop back to step 05 (max 2 iterations → human escalation via
  punch-out E).

## Punch-Out Points

Explicit human decision points where the workflow must stop for sign-off:

| ID | Trigger | Raised by | File | Human decision |
|----|---------|-----------|------|----------------|
| A | claims that cannot be confirmed/fixed | G4 | `audit/punchout-unable-to-validate.md` | mark each `still-present` / `removed` / `defer` |
| B | CIS/NIST reference changes flagged upstream | G5 check | `audit/punchout-framework-mappings.md` | approve each changed mapping |
| C | regressed findings carried into report | G5 check | `audit/punchout-regressions.md` | confirm evidence supports each regression |
| D | added/removed report sections | G5 check | `audit/punchout-structure-changes.md` | approve/reject each structural change |
| E | G4/G5 loop exhaustion | G4/G5 | `audit/punchout-log.md` | human resolves stalled claims/tone directly |

Punch-outs A–D are raised as files in `audit/` and recorded as
`triggered: true` in `run-state.json`; the workflow cannot proceed past the
gate until a human sets `approved: true` — an automated "fail workflow" is
distinct from this manual "punch to human" halt.

**Bypass testing (required evidence):** after each punch-out fires, someone
must attempt to continue the workflow without approval and confirm the gate
blocks them. Every halt, bypass attempt, and human decision is recorded in
`audit/punchout-log.md` (see the `bypass test performed` / `bypass result`
columns). Punch-out points that exist on paper but were never tested do NOT
qualify.

## Audit Trail

`audit/run-YYYYMMDD.jsonl` — one record per step:

```json
{"step": "05-tone-consistency", "model": "...", "input_hashes": {...}, "output_hashes": {...},
 "tokens_in": 0, "tokens_out": 0, "cost_usd": 0.0, "finding_ids": ["F-001"],
 "guardrail_inbound": {"id": "G4", "result": "pass"}}
```

Each record identifies the step, the model used, input/output content
hashes, token counts, and cost — so any failure can be traced to its exact
origin step, and per-step model/token/cost coverage spans every step in the
pipeline.

## End-to-End Success Metric

A run succeeds iff:
1. G5 passes with zero unresolved claims,
2. every triggered punch-out is resolved with a recorded human decision, and
3. the final report is emitted.

Per-run result appended to `audit/success-log.md`:

```
| date | run id | result | punch-outs | iterations | notes |
```

Success rate = successful runs / total runs, measured across the full
workflow start to finish and tracked quarter over quarter to show stability
or improvement.
