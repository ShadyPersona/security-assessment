# Workflow Definition — Tone & Finalize Pipeline

A two-step sequential workflow that takes ONE report draft through tone
normalization (against a template and two prior quarters' reports) and final
delivery, with one fail-closed deterministic guardrail on every handoff,
human punch-out gates, and a structured audit trail. Fully self-contained in
this folder — no paths, files, or skills outside `tone-assessment/` are
referenced.

## Pipeline

```
[inputs: draft.md + template + 2 prior reports]
        │
        ▼  G4 (input-completeness gate)
05-tone-consistency ──G5──▶ 06-finalize → [deliverable]
        │                        │
        └── missing-content ─────┴── punch-out → human sign-off → resume
```

## Inputs

All inputs are placed in `runs/<YYYY-QN>/inputs/` before the run starts.
Exactly ONE document is modified; the three references are read-only.

| # | Source | Role |
|---|--------|------|
| 1 | `draft.md` | The document being toned — the only file rewritten |
| 2 | `template` | AUTHORITATIVE for section order, headings, formatting, required elements; wins every conflict |
| 3 | `report-prev-quarter` | Voice, tense, phrasing-convention evidence |
| 4 | `report-2-quarters-ago` | Style-consistency trend evidence |
| 5 | `run-state.json` | Copied from `templates/` at run start; loop counters + punch-out flags |

## Step Contract

Each step MUST run its inbound guardrail check and append the result to
`audit/guardrails.jsonl` before doing any work. A failed gate halts the
workflow.

## Run Layout

- Working artifacts live in `runs/<YYYY-QN>/` (see `runs/README.md`).
- `templates/run-state.json` tracks `loop_counters` (G4/G5, max 2) and
  punch-out `approved` status.
- `tone-changes.json` MUST validate against `schemas/tone-changes.schema.json` —
  schema validation is what makes the guardrails deterministic.
- Cross-run evidence lives in `audit/` (punch-outs, guardrails.jsonl,
  success-log.md, run-*.jsonl).

| Step | Skill | Consumes | Produces |
|------|-------|----------|----------|
| 05 | tone-consistency | `draft.md`, `template`, both prior reports | `toned.md`, `tone-changes.json` |
| 06 | finalize | `toned.md`, `draft.md`, `template`, both prior reports, `run-state.json`, audit artifacts | Final report, `audit-report.md` |

## Guardrails

Guardrails sit **between** workflow steps (and inbound on step 05) — they are
deterministic checks run before the next agent is allowed to proceed, not
review performed inside a step. All are automated.

### G4 — inputs → tone (Input-Completeness Gate, enforced by step 05)
- All four input files exist and are readable; SHA-256 recorded per file.
- `run-state.json` present; template yields an extractable section/heading
  structure.
- Missing/unreadable input = hard fail, no `toned.md` produced.
- Failure → increment `loop_counters.G4`; at `loop_max` (2) escalate to
  human via punch-out E in `audit/punchout-log.md`.

### G5 — tone → finalize (Tone, Fact-Preservation & Accountability Gate, enforced by step 06)
- Tone check: `toned.md` conforms to the template (required sections
  present/ordered, headings, formatting) and prior-report conventions
  (voice, tense, phrasing; banned patterns absent).
- Fact-preservation diff: every number, date, percentage, CIS/NIST
  reference, and finding title in `toned.md` must exist in `draft.md`.
- Change accountability: every draft→toned edit recorded in
  `tone-changes.json` (schema-validated). Unrecorded edit = failure.
- Punch-out closure: every `triggered: true` punch-out must be
  `approved: true`. Unapproved = hard fail — STOP for human sign-off.
- Failure → loop back to step 05 (max 2 iterations → human escalation).

## Punch-Out Points

Explicit human decision points where the workflow must stop for sign-off:

| ID | Trigger | Raised by | File | Human decision |
|----|---------|-----------|------|----------------|
| A | template requires a section/element `draft.md` has no content for | step 05 | `audit/punchout-missing-content.md` | provide content / drop section / defer |
| E | G4 or G5 loop exhaustion (max 2) | G4/G5 | `audit/punchout-log.md` | human resolves stalled input/tone directly |

A punch-out halt is a manual "punch to human" — distinct from an automated
guardrail failure, which fails the workflow without human involvement.

**Bypass testing (required evidence):** after each punch-out fires, someone
must attempt to continue the workflow without approval and confirm the gate
blocks them. Every halt, bypass attempt, and human decision is recorded in
`audit/punchout-log.md` (see `bypass test performed` / `bypass result`
columns). Punch-out points never tested do NOT qualify.

## Audit Trail

`audit/run-YYYYMMDD.jsonl` — one record per step:

```json
{"step": "05-tone-consistency", "model": "...", "input_hashes": {...}, "output_hashes": {...},
 "tokens_in": 0, "tokens_out": 0, "cost_usd": 0.0,
 "guardrail_inbound": {"id": "G4", "result": "pass"}}
```

Each record identifies the step, model, input/output content hashes, token
counts, and cost — any failure traces to its exact origin step, with
per-step model/token/cost coverage across the whole pipeline.

## End-to-End Success Metric

A run succeeds iff:
1. G5 passes (tone, fact diff, change accountability, punch-out closure),
2. every triggered punch-out is resolved with a recorded human decision, and
3. the final report is emitted.

Per-run result appended to `audit/success-log.md`:

```
| date | run id | result | punch-outs | iterations | notes |
```

Success rate = successful runs / total runs, measured across the full
workflow start to finish and tracked quarter over quarter to show stability
or improvement.
