# Workflow Definition — Assessment Synthesis & Finalize Pipeline (V2)

A two-step sequential workflow that takes ONE report draft through
security-assessment synthesis — converting telemetry and control data into
conclusion-led quarter-over-quarter analysis under a strict substance
freeze (professional writing standards + an authoritative template) — and
final delivery, with
one fail-closed deterministic guardrail on every handoff, human punch-out
gates, and a structured audit trail. Fully self-contained in this folder —
no paths, files, or skills outside `tone-assessmentV2/` are referenced.

## Pipeline

```
[inputs: draft.md + template]
        │
        ▼  G4 (input-completeness gate)
05-tone-consistency ──G5──▶ 06-adversarial-review → [deliverable]
        │                            │
        └── missing-content ─────────┴── punch-out → human sign-off → resume
```

## Inputs

All inputs are placed in `runs/<run-id>/inputs/` before the run starts.
Exactly ONE document is modified; the template is read-only.

| # | Source | Role |
|---|--------|------|
| 1 | `draft.md` | The document being edited — the only file rewritten |
| 2 | `template` | AUTHORITATIVE for section order, headings, formatting, required elements; wins every conflict |
| 3 | `run-state.json` | Copied from `templates/` at run start; loop counters + punch-out flags |

## Step Contract

Each step MUST run its inbound guardrail check and append the result to
`audit/guardrails.jsonl` before doing any work. A failed gate halts the
workflow.

## Run Layout

- Working artifacts live in `runs/<run-id>/` (see `runs/README.md`).
- `templates/run-state.json` tracks `loop_counters` (G4/G5, max 2) and
  punch-out `approved` status.
- `tone-changes.json` MUST validate against `schemas/tone-changes.schema.json` —
  schema validation is what makes the guardrails deterministic.
- Cross-run evidence lives in `audit/` (punch-outs, guardrails.jsonl,
  success-log.md, run-*.jsonl).

| Step | Skill | Consumes | Produces |
|------|-------|----------|----------|
| 05 | tone-consistency | `draft.md`, `template` | `toned.md`, `tone-changes.json` |
| 06 | adversarial-review | `toned.md`, `draft.md`, `template`, `tone-changes.json`, `run-state.json`, audit artifacts | Final report, `audit-report.md` |

## Guardrails

Guardrails sit **between** workflow steps (and inbound on step 05) — they are
deterministic checks run before the next agent is allowed to proceed, not
review performed inside a step. All are automated.

### G4 — inputs → tone (Input-Completeness Gate, enforced by step 05)
- Both input files exist and are readable; SHA-256 recorded per file.
- `run-state.json` present; template yields an extractable section/heading
  structure.
- Missing/unreadable input = hard fail, no `toned.md` produced.
- Failure → increment `loop_counters.G4`; at `loop_max` (2) escalate to
  human via punch-out E in `audit/punchout-log.md`.

### G5 — tone → adversarial-review (Adversarial Review Gate: Assessment Quality, Substance Freeze & Accountability, enforced by step 06)
- Assessment-quality check — did the rewrite produce real analysis:
  `toned.md` conforms to the template AND every substantive section is
  conclusion-first assessment (condition, quarter movement, evidence,
  significance, qualification, recommended response, calibration); domains with
  comparable evidence carry an explicit quarter-over-quarter thesis
  (preceding vs current condition, material movement, attribution,
  carried-in unresolved and newly material findings — post-quarter
  evidence never presented as reporting-quarter progress); related
  controls are synthesized into assessment themes before individual
  controls are discussed; professional and client-consultative standards
  met (voice, tense, terminology, cadence, hedging, banned patterns
  absent, spelling/grammar/punctuation correct, inclusive and neutral
  language, no sensitive geopolitical references); every sentence has a
  clear subject and verb and states one idea, including inside table
  narrative cells; semicolons connect only closely related clauses;
  evidence gaps are integrated as supporting context, not left as raw
  fragments; scope is stated in plain language; every substantive finding
  or risk is paired with a concrete client action; sections lead with
  supported conclusions rather than methodology or evidence limitations;
  technical findings are interpreted for the client rather than restated;
  concise consultative prose without defensive caveat-stacking; every
  substantive domain answers what was assessed, what changed, why it
  matters, and what's next; significance is tied to the client's business
  risk; movement claims are temporally anchored; positive findings are
  calibrated with their limitations; no noun stacks or product-vendor
  grammar remain; table captions, row labels, and narrative cells are
  reviewed for banned patterns, vague temporal terms, and sentence
  discipline; every draft table is accounted for in
  `tone-changes.json#tables_reviewed`. A data-first, unchanged, merely
  word-patched, or table-unreviewed draft fails even with zero banned
  patterns present.
- Substance freeze check: every number, date, percentage, measurement,
  finding title, CIS/NIST reference, classification outcome, piece of
  evidence, and recommendation in `toned.md` — including those inside
  tables — must exist in `draft.md` unchanged; re-written/re-scoped
  evidence, invented facts in narrative framing, shifted table-cell
  meaning, or meaning-shifting paraphrase fails.
- Change accountability: every draft→toned edit recorded in
  `tone-changes.json` (schema-validated) with `reason`,
  `assessment_function`, and `facts_changed: false`; table edits verified
  row by row; every draft table listed in `tables_reviewed`; draft issues
  surfaced in `observations`. A change log with no analytical
  `assessment_function` coverage fails.
- Punch-out closure: every `triggered: true` punch-out must be
  `approved: true`. Unapproved = hard fail — STOP for human sign-off.
- Step 06 acts as the adversarial reviewer of step 05 and only assembles the final deliverable after G5 passes.
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
1. G5 passes (assessment quality, substance freeze, change accountability,
   punch-out closure),
2. every triggered punch-out is resolved with a recorded human decision,
3. the final report is emitted, and
4. the run result is appended to `audit/success-log.md` and step records to
   `audit/run-YYYYMMDD.jsonl`.

Per-run result appended to `audit/success-log.md`:

```
| date | run id | result | punch-outs | iterations | notes |
```

Success rate = successful runs / total runs, measured across the full
workflow start to finish and tracked quarter over quarter to show stability
or improvement.
