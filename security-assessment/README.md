# Security Assessment Workflow

Sequential multi-skill pipeline that updates a client's quarterly security
assessment from Microsoft Defender exports, with a fail-closed guardrail on
every handoff, human punch-out gates, and a full audit trail.

**Output:** an updated quarterly assessment report preserving the prior
report's format and style, reflecting the current-quarter Defender data.

Start here: [`WORKFLOW.md`](./WORKFLOW.md)

## Inputs (5 per run)

| # | Source | Role |
|---|--------|------|
| 1 | Previous quarter's report | Baseline document to update (format + content) |
| 2 | Report from two quarters ago | Historical context & update methodology |
| 3 | Defender export folder | Authoritative current-quarter security state |
| 4 | CIS M365 Foundations Benchmark PDF | Framework reference validation |
| 5 | NIST CSF 2.0 (CSWP.29) PDF | Framework reference validation |

## Layout

```
security-assessment/
├── WORKFLOW.md          # pipeline definition, step contract, guardrails G1–G5, punch-outs
├── README.md            # this file
├── skills/              # the six ordered sub-skills (see below)
├── schemas/             # JSON Schemas every artifact must validate against
├── templates/           # run-state.json seed — copy into each new run dir
├── runs/                # one directory per quarterly run (see runs/README.md)
└── audit/               # cross-run evidence: punch-outs, guardrails.jsonl, success-log, run-*.jsonl
```

## Running a quarter

1. Create `runs/<YYYY-QN>/` and copy `templates/run-state.json` into it.
2. Place the 5 inputs in `runs/<YYYY-QN>/inputs/` (or record their paths).
3. Execute the skills **in order** — each SKILL.md begins with the inbound
   guardrail it must run and log to `audit/guardrails.jsonl` before working:

| # | Skill | Inbound gate |
|---|-------|--------------|
| 01 | `skills/01-ingest` | — (establishes ground truth) |
| 02 | `skills/02-findings-matrix` | G1 input completeness |
| 03 | `skills/03-draft-update` | G2 evidence citation + punch-outs A, C |
| 04 | `skills/04-adversarial-review` | G3 structure/framework diff + punch-outs B, D |
| 05 | `skills/05-tone-consistency` | G4 zero unresolved claims |
| 06 | `skills/06-finalize` | G5 tone + fact preservation |

4. When a punch-out file is generated, the workflow halts. Record the human
   decision in the file under `audit/`, mark it APPROVED, update
   `run-state.json`, then resume the blocked step.
5. Update `run-state.json`'s `loop_counters` whenever G4 or G5 sends work
   back — at 2 iterations, escalate to a human (punch-out E).

## Punch-out legend

| ID | Triggered when | Human decides |
|----|----------------|---------------|
| A | findings can't be validated by current data | still-present / removed / defer |
| B | CIS/NIST reference mappings changed | approve each change |
| C | findings classified `regressed` | confirm evidence supports it |
| D | report sections added/removed | approve/reject each change |
| E | G4 or G5 loop exhausted (2 iterations) | resolve directly |

## Artifact ↔ schema map

| Artifact | Schema | Produced by | Checked by |
|----------|--------|-------------|------------|
| `inventory.json` | `schemas/inventory.schema.json` | 01 | G1 |
| `evidence-index.json` | `schemas/evidence-index.schema.json` | 01 | G1 |
| `style-profile.json` | `schemas/style-profile.schema.json` | 01 | G1, G5 |
| `matrix.json` | `schemas/matrix.schema.json` | 02 | G2, G4 |
| `draft-map.json` | `schemas/draft-map.schema.json` | 03 | G3, G4 |
| `review.json` | `schemas/review.schema.json` | 04 | G4 |
| `tone-changes.json` | `schemas/tone-changes.schema.json` | 05 | G5 |
