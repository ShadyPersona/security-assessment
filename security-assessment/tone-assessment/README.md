# tone-assessment

Self-contained two-step workflow that takes a reviewed security-assessment
draft through tone normalization (`05-tone-consistency`) and final delivery
(`06-finalize`). Extracted from the six-step quarterly assessment pipeline;
**no dependencies outside this folder**.

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
├── schemas/                   ← JSON Schemas making guardrails deterministic
│   ├── review.schema.json         (G4 input gate)
│   ├── draft-map.schema.json      (G4 coverage check)
│   ├── style-profile.schema.json  (tone target, G5 tone check)
│   └── tone-changes.schema.json   (step 05 output contract)
├── templates/run-state.json   ← loop counters + punch-out approval flags
├── runs/README.md             ← per-run directory layout
└── audit/                     ← cross-run evidence (append-only)
    ├── guardrails.jsonl           (one record per guardrail check)
    ├── punchout-log.md            (halts, bypass tests, human decisions)
    ├── punchout-unable-to-validate.md
    ├── punchout-framework-mappings.md
    ├── punchout-regressions.md
    ├── punchout-structure-changes.md
    └── success-log.md             (end-to-end run results + success rate)
```

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
   place the inputs (see `WORKFLOW.md` §Inputs) in `runs/<YYYY-QN>/inputs/`.
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
