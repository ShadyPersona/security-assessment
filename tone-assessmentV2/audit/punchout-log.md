# Punch-Out Log

Records every workflow halt for human decision, the bypass test performed
against it, and the resolution. Required for Stage 4 punch-out evidence.

> The two data rows below are **EXAMPLES / TEMPLATES** showing the expected format and bypass tests. Stage 4 certification requires these to be replaced with records from actual bypass tests.

| date | run id | punch-out | trigger | bypass test performed | bypass result | human decision | resolved by | resolved at |
|------|--------|-----------|---------|----------------------|---------------|----------------|-------------|-------------|
| 2026-10-21 | EXAMPLE-2026-Q3-Bethany-A | A: missing-content | template requires a section the draft has no content for | attempted step 05 continuation / step 06 without sign-off | blocked by punch-out flag → G5 hard fail | approve / reject / edit template | | |
| 2026-10-21 | EXAMPLE-2026-Q3-Bethany-E | E: loop-exhaustion (G4/G5) | loop_max (2) reached on G4 or G5 | attempted additional retry past loop_max | halted for human by run-state.json loop_max | approve manual rewrite / reject run | | |
