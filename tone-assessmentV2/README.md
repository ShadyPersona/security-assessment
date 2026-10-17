# tone-assessmentV2

Self-contained two-step workflow that performs **security-assessment
synthesis** on ONE report draft (`draft.md`) — converting telemetry and
control data into conclusion-led quarter-over-quarter analysis — under a
strict substance freeze, then assembles the final deliverable.
**No dependencies outside this folder.**

V2 differs from `tone-assessment/`: the agent is a security-assessment
synthesizer applying professional analytical + writing standards directly —
no prior-quarter reports are required as inputs, and success is measured by
assessment quality (conclusion-led analysis, quarterly thesis, cross-signal
synthesis) and client-consultative usefulness, not polished language alone.

## Layout

```
tone-assessmentV2/
├── README.md                  ← this file (guide to the artifacts)
├── WORKFLOW.md                ← workflow definition (pipeline, guardrails, punch-outs, success metric)
├── .github/skills/
│   ├── 05-tone-consistency/
│   │   ├── SKILL.md           ← step 05 agent prompt (enforces inbound G4)
│   │   ├── ITERATION_LOG.md   ← eval-driven prompt evolution (EDD evidence)
│   │   └── evals/promptfooconfig.yaml
│   ├── 06-adversarial-review/
│   │   ├── SKILL.md           ← step 06 agent prompt (G5 adversarial review + finalize on pass)
│   │   ├── ITERATION_LOG.md
│   │   └── evals/promptfooconfig.yaml
│   └── tone-assessmentv2-orchestrator/
│       └── SKILL.md           ← single-file orchestrator for agent use (combines 05 + 06 + workflow)
├── schemas/
│   └── tone-changes.schema.json   (step 05 output contract, checked at G5)
├── templates/run-state.json   ← loop counters + punch-out approval flags
├── runs/README.md             ← per-run directory layout (run-id named, 2 inputs)
└── audit/                     ← cross-run evidence (append-only)
    ├── guardrails.jsonl           (one record per guardrail check)
    ├── punchout-log.md            (halts, bypass tests, human decisions)
    ├── punchout-missing-content.md
    └── success-log.md             (end-to-end run results + success rate)
```

## Inputs per run (place in `runs/<YYYY-QN>/inputs/`)

1. `draft.md` — the document being edited (the only file modified)
2. `template` — authoritative for structure/formatting; wins conflicts

## Stage 4 artifact map

| Required artifact | Where it lives |
|-------------------|----------------|
| Workflow definition | `WORKFLOW.md` — both agents wired end-to-end, handoffs + loop-back branching documented |
| Guardrails | `WORKFLOW.md` §Guardrails; G4 inbound on step 05, G5 between 05→06; schema-validated, deterministic, automated |
| Punch-out evidence | `audit/punchout-*.md` + `audit/punchout-log.md` — human decision points with bypass-test columns; `run-state.json` blocks unapproved punch-outs at G5 |
| End-to-end success rate | `audit/success-log.md` — full-workflow result per run + cumulative rate |
| Audit trail | `audit/guardrails.jsonl` + `audit/run-YYYYMMDD.jsonl` — per-step model, hashes, tokens, cost; failure traceable to origin step |

## Running the workflow

1. Create `runs/<run-id>/`, copy `templates/run-state.json` into it, and
   place the two inputs in `runs/<run-id>/inputs/`.
2. Invoke skill `05-tone-consistency` → produces `toned.md`,
   `tone-changes.json`, and the G4 record.
3. Invoke skill `06-adversarial-review` → runs the G5 adversarial review of
   step 05's output, assembles the deliverable on pass, and closes the audit
   trail.
4. Record the run outcome in `audit/success-log.md`.

## Stage 4 certification guide

For an examiner reviewing this workflow, the evidence lives in:

| Stage 4 artifact | File to inspect |
|---|---|
| Workflow definition | `WORKFLOW.md` |
| Adversarial review agent | `.github/skills/06-adversarial-review/SKILL.md` Phase 1 |
| Guardrail logs | `audit/guardrails.jsonl` |
| Punch-out evidence | `audit/punchout-log.md` + `audit/punchout-missing-content.md` |
| End-to-end success rate | `audit/success-log.md` |
| Per-step audit trail | `audit/run-YYYYMMDD.jsonl` |
| Prompt evolution / EDD | `.github/skills/05-tone-consistency/ITERATION_LOG.md`, `.github/skills/06-adversarial-review/ITERATION_LOG.md` |
| Version lineage | `VERSION_HISTORY.md` + Git tags `tone-assessment-v1` and `tone-assessment-v2` |

**Note:** Promptfoo evals and real Copilot runs require populated API keys and actual executions. The skill configs and log formats are ready; the pass/fail results and run records must be produced by running the workflow.

## Evals

Each skill ships a Promptfoo config under `evals/`. Run from the skill's
`evals/` directory after setting `OPENAI_API_KEY` and `ANTHROPIC_API_KEY`:

```
promptfoo eval
```

Record the summary in the corresponding `ITERATION_LOG.md`.

