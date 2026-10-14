# Punch-Out Log

Records every workflow halt for human decision, the bypass test performed
against it, and the resolution. Required for Stage 4 punch-out evidence.

| date | run id | punch-out | trigger | bypass test performed | bypass result | human decision | resolved by | resolved at |
|------|--------|-----------|---------|----------------------|---------------|----------------|-------------|-------------|
| | | A: unable-to-validate | `unable-to-validate` claims in review.json | attempted step 05 without sign-off | blocked by G4 | | | |
| | | B: framework-mappings | changed CIS/NIST refs flagged upstream | attempted step 06 without sign-off | blocked by G5 | | | |
| | | C: regressions | `regressed` findings carried into report | attempted step 06 without sign-off | blocked by G5 | | | |
| | | D: structure-changes | added/removed report sections | attempted step 06 without sign-off | blocked by G5 | | | |
| | | E: loop-exhaustion (G4/G5) | loop_max (2) reached on G4 or G5 | attempted additional retry past loop_max | halted for human | | | |
