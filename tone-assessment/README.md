# tone-assessment

Self-contained two-step workflow that rewrites ONE report draft
(`draft.md`) for tone/style — against a template (authoritative) and the two
prior quarters' reports — then assembles the final deliverable.
**No dependencies outside this folder.**

## Layout

```
tone-assessment/
├── README.md                  ← this file (guide to the artifacts)
├── WORKFLOW.md                ← workflow definition (pipeline, guardrails, punch-outs, success metric)
├── .github/skills/
│   ├── 05-tone-consistency/
│   │   ├── SKILL.md           ← step agent prompt (enforces inbound G4)
│   │   ├── ITERATION_LOG.md   ← eval-driven prompt evolution (EDD evidence)
│   │   └── evals/promptfooconfig.yaml
│   └── 06-finalize/
│       ├── SKILL.md           ← step agent prompt (enforces inbound G5)
│       ├── ITERATION_LOG.md
│       └── evals/promptfooconfig.yaml
├── schemas/
│   └── tone-changes.schema.json   (step 05 output contract, checked at G5)
├── templates/run-state.json   ← loop counters + punch-out approval flags
├── runs/README.md             ← per-run directory layout (4 inputs)
└── audit/                     ← cross-run evidence (append-only)
    ├── guardrails.jsonl           (one record per guardrail check)
    ├── punchout-log.md            (halts, bypass tests, human decisions)
    ├── punchout-missing-content.md
    └── success-log.md             (end-to-end run results + success rate)
```

## Inputs per run (place in `runs/<YYYY-QN>/inputs/`)

1. `draft.md` — the document being toned (the only file modified)
2. `template` — authoritative for structure/formatting; wins conflicts
3. `report-prev-quarter` — voice/tense/phrasing evidence
4. `report-2-quarters-ago` — style-trend evidence

## Stage 4 artifact map

| Required artifact | Where it lives |
|-------------------|----------------|
| Workflow definition | `WORKFLOW.md` — both agents wired end-to-end, handoffs + loop-back branching documented |
| Guardrails | `WORKFLOW.md` §Guardrails; G4 inbound on step 05, G5 between 05→06; schema-validated, deterministic, automated |
| Punch-out evidence | `audit/punchout-*.md` + `audit/punchout-log.md` — human decision points with bypass-test columns; `run-state.json` blocks unapproved punch-outs at G5 |
| End-to-end success rate | `audit/success-log.md` — full-workflow result per run + cumulative rate |
| Audit trail | `audit/guardrails.jsonl` + `audit/run-YYYYMMDD.jsonl` — per-step model, hashes, tokens, cost; failure traceable to origin step |

## Running the workflow

1. Create `runs/<YYYY-QN>/`, copy `templates/run-state.json` into it, and
   place the four inputs in `runs/<YYYY-QN>/inputs/`.
2. Invoke skill `05-tone-consistency` → produces `toned.md`,
   `tone-changes.json`, and the G4 record.
3. Invoke skill `06-finalize` → runs G5, assembles the deliverable, and
   closes the audit trail.
4. Record the run outcome in `audit/success-log.md`.

## Evals

Each skill ships a Promptfoo config under `evals/`. Run from the skill's
`evals/` directory:

```
promptfoo eval
```
