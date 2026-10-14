# Punch-Out Log

Records every workflow halt for human decision, the bypass test performed
against it, and the resolution. Required for Stage 4 punch-out evidence.

| date | run id | punch-out | trigger | bypass test performed | bypass result | human decision | resolved by | resolved at |
|------|--------|-----------|---------|----------------------|---------------|----------------|-------------|-------------|
| | | A: unable-to-validate | | attempted step 03 without sign-off | blocked by G2 | | | |
| | | B: framework-mappings | | attempted step 04 without sign-off | blocked by G3 | | | |
| | | C: regressions | | attempted step 03 without sign-off | blocked by G2 | | | |
| | | D: structure-changes | | attempted step 04 without sign-off | blocked by G3 | | | |
| | | E: loop-exhaustion (G4/G5) | | guardrail max-iterations hit | halted for human | | | |
