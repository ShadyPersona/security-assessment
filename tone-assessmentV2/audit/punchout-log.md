# Punch-Out Log

Records every workflow halt for human decision, the bypass test performed
against it, and the resolution. Required for Stage 4 punch-out evidence.

| date | run id | punch-out | trigger | bypass test performed | bypass result | human decision | resolved by | resolved at |
|------|--------|-----------|---------|----------------------|---------------|----------------|-------------|-------------|
| | | A: missing-content | template requires a section the draft has no content for | attempted step 05 continuation / step 06 without sign-off | blocked by punch-out flag → G5 hard fail | | | |
| | | E: loop-exhaustion (G4/G5) | loop_max (2) reached on G4 or G5 | attempted additional retry past loop_max | halted for human | | | |
