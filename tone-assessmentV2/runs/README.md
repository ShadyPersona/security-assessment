# runs/

One directory per workflow run, named by run ID, e.g. `runs/2026-Q3-IRONTRAX/`.
Run IDs are supplied by the user; they may include quarter, client, or test suffix.
All working artifacts live inside the run directory — never in the repo root.

```
runs/2026-Q3-IRONTRAX/
├── inputs/
│   ├── draft.md              # the ONE document being edited (input to step 05)
│   └── template.*            # AUTHORITATIVE structure/formatting reference
├── toned.md              # step 05 output
├── tone-changes.json     # step 05 output → ../schemas/tone-changes.schema.json
├── audit-report.md       # step 06 output (run summary)
├── run-state.json        # copied from templates/, updated each step
└── final-report.<ext>    # step 06 output (client deliverable, template format)
```

`tone-changes.json` must validate against `../schemas/tone-changes.schema.json` — the guardrails check this at the G5 handoff. The schema requires `tables_reviewed` and `observations` arrays in addition to `changes`.

Cross-run evidence (punch-outs, guardrail results, success log, per-step audit records) lives in `../audit/`, which accumulates across runs.
