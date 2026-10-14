# Security Assessment Workflow — Repository Instructions

This repository implements a deterministic, audited, multi-step pipeline for updating a quarterly Microsoft 365 security assessment report. Copilot must follow these instructions whenever it works in this repository.

## Project purpose

Generate an updated quarterly security assessment report that:
- Reflects the current quarter's Microsoft Defender exports.
- Preserves the prior report's structure, tone, and formatting.
- Validates every factual claim against evidence.
- Maintains a complete audit trail and human punch-out gates.

## Repository layout

```
security-assessment/
├── .github/
│   ├── copilot-instructions.md          # this file
│   ├── security-assessment-orchestrator.agent.md  # end-to-end orchestrator agent
│   └── skills/                          # six ordered sub-skills (Copilot-discoverable)
│       ├── 01-ingest/
│       ├── 02-findings-matrix/
│       ├── 03-draft-update/
│       ├── 04-adversarial-review/
│       ├── 05-tone-consistency/
│       └── 06-finalize/
├── WORKFLOW.md          # pipeline definition, step contract, guardrails G1–G5, punch-outs
├── README.md            # quickstart and artifact map
├── schemas/             # JSON Schemas every artifact must validate against
├── templates/           # run-state.json seed — copy into each new run dir
├── runs/                # one directory per quarterly run (see runs/README.md)
└── audit/               # cross-run evidence: punch-outs, guardrails.jsonl, success-log, run-*.jsonl
```

## Non-negotiable rules

1. **Skill order is fixed.** Always run the six skills in this order:
   `01-ingest → 02-findings-matrix → 03-draft-update → 04-adversarial-review → 05-tone-consistency → 06-finalize`.
2. **Each step runs its inbound guardrail first.** Results are appended to `audit/guardrails.jsonl`.
3. **A failed guardrail is a hard stop.** Do not proceed past a failure.
4. **Punch-outs require human approval.** When `audit/punchout-*.md` is generated, stop and ask the human. Never mark a punch-out approved yourself.
5. **Evidence is authoritative.** Current-quarter Defender exports determine current state. Do not assume a prior condition still exists without evidence.
6. **Facts must not be fabricated.** Every metric, date, percentage, framework mapping, and finding must be traceable to an evidence file.
7. **Schema validation is mandatory.** Every JSON artifact must validate against the corresponding file in `schemas/`.
8. **Preserve tone and format.** Use `style-profile.json` from step 01 as the target for all report prose.

## How Copilot should help

- **Interactive runs:** Use the `security-assessment-orchestrator` agent. It loads the six skills and enforces ordering and guardrails.
- **Single-step help:** Load only the relevant skill from `.github/skills/<skill-name>/SKILL.md` and do not advance beyond that step's outputs.
- **Editing skills:** When modifying any `SKILL.md`, also update its `evals/promptfoo.yaml` and `ITERATION_LOG.md` to maintain Stage 3 compliance.
- **Running evaluations:** Use `promptfoo eval` inside `.github/skills/<skill-name>/` to validate prompt quality. Target score for every criterion is >=95% on mid-tier models.

## Outputs that require human review before delivery

- `audit/punchout-unable-to-validate.md`
- `audit/punchout-regressions.md`
- `audit/punchout-framework-mappings.md`
- `audit/punchout-structure-changes.md`
- The final client deliverable produced by `06-finalize`.
