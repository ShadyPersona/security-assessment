# runs/

One directory per workflow run, named by quarter, e.g. `runs/2026-Q4/`.
All working artifacts live inside the run directory — never in the repo root.

```
runs/2026-Q4/
├── inputs/               # copies of the source documents for this run
│   ├── prior-report.*    # previous quarter's report (style + format baseline)
│   ├── reviewed.md       # output of the upstream review step (input to step 05)
│   ├── review.json       # claim verdicts from upstream adversarial review
│   ├── draft-map.json    # section → matrix-row mapping from upstream drafting
│   └── style-profile.json# voice/tense/conventions extracted from prior report
├── toned.md              # step 05 output
├── tone-changes.json     # step 05 output
├── audit-report.md       # step 06 output (run summary)
├── run-state.json        # copied from templates/, updated each step
└── final-report.<ext>    # step 06 output (client deliverable)
```

Every JSON artifact must validate against the corresponding file in
`../schemas/` — guardrails check this at each handoff.

Cross-run evidence (punch-outs, guardrail results, success log, per-step
audit records) lives in `../audit/`, which accumulates quarter over quarter.
