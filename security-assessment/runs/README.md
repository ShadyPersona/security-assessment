# runs/

One directory per workflow run, named by quarter, e.g. `runs/2026-Q3/`.
All working artifacts live inside the run directory — never in the repo root.

```
runs/2026-Q3/
├── inputs/               # copies or symlinks of the 5 source documents
├── inventory.json        # step 01 output
├── evidence-index.json   # step 01 output
├── style-profile.json    # step 01 output
├── matrix.json           # step 02 output
├── draft.md              # step 03 output
├── draft-map.json        # step 03 output
├── review.json           # step 04 output
├── reviewed.md           # step 04 output
├── toned.md              # step 05 output
├── tone-changes.json     # step 05 output
├── run-state.json        # copied from templates/, updated each step
└── final-report.<ext>    # step 06 output (client deliverable)
```

Every JSON artifact must validate against the corresponding file in
`../schemas/` — guardrails check this at each handoff.

Cross-run evidence (punch-outs, guardrail results, success log, per-step
audit records) lives in `../audit/`, which accumulates quarter over quarter.
