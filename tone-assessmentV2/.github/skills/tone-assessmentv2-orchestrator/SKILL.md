---
name: tone-assessmentv2-orchestrator
description: Authoritative instructions for running the tone-assessmentV2 workflow (steps 05 + 06) — security-assessment synthesis of one draft document against a template, under a substance freeze, with guardrails G4/G5, punch-outs, and a full audit trail.
---

# tone-assessmentV2 — Orchestrator Instructions

You are running a two-step security-assessment synthesis workflow. This file
is the authoritative definition. If companion files exist
(`WORKFLOW.md`, `.github/skills/05-tone-consistency/SKILL.md`,
`.github/skills/06-adversarial-review/SKILL.md`, `schemas/tone-changes.schema.json`,
`templates/run-state.json`), read them for confirmation — but if any file is
unreadable or ambiguous, THIS document governs. Never approximate or
substitute a file whose identity you cannot confirm; state the problem and
stop.

## Inputs

Exactly two documents, supplied by the user:

| File | Role |
|------|------|
| `draft.md` (or attached document) | The document being edited — the ONLY file rewritten. Factual source and substantive boundary |
| `template` | AUTHORITATIVE for section order, headings, formatting, required elements. Wins every conflict |

If the user supplies only one document, ask whether it serves as both draft
and template before proceeding.

## Step 05 — Assessment Synthesis (run first)

**G4 — input-completeness gate (do this before ANY editing):**
- Both inputs exist and are readable; the template yields an extractable
  section/heading structure.
- Failure = hard stop. Report `G4 failure` with the exact cause. Produce nothing.

**Then rewrite `draft.md` → `toned.md`:**

1. **Assessment-narrative evaluation.** Every substantive section must be
   conclusion-first, containing all applicable elements: Condition,
   Quarter movement, Evidence, Significance, Qualification, Recommended
   response, Calibration. A table, score, export value, Microsoft recommendation, or
   CIS/NIST mapping is NOT analysis. Explicit failure conditions: data
   without conclusion, metrics without significance, findings without
   quarterly context, recommendations without analytical traceability,
   positive findings without calibrated limitations. Significance must
   answer "so what?" for the client's business risk.
   Every positive finding must be calibrated with what it does and does
   not prove; every risk must state what is known and unknown.
   **Security Awareness exemption:** sections whose heading (case-insensitive)
   matches "Security Awareness" are EXCLUDED from the conclusion-first
   analytical rewrite. Preserve their existing content and facts unchanged;
   apply only template formatting alignment and banned-pattern removal. Do
   not infer quarter movement, significance, or cross-theme relationships
   for this section. Rewrite data-first passages into conclusion-first prose
   for all non-exempt sections.
2. **Quarterly review requirement.** For each domain with comparable
   historical evidence in the draft, establish: preceding vs current
   condition, material movement, attribution (verified
   improvement/regression, measurement movement, or unattributable),
   carried-in unresolved findings, newly material findings. Post-quarter
   evidence must not be presented as reporting-quarter progress. Where
   comparison is unsupported, say so explicitly — never manufacture
   movement. Anchor every movement claim to the specific quarter or
   observation period in the evidence; avoid vague temporal terms such as
   "recently," "currently," or "this period" unless the evidence names the
   date.
3. **Cross-signal synthesis.** Analyze related controls as themes —
   authentication/privileged access; legacy auth/access-policy;
   information protection; external collaboration/data egress; endpoint
   exposure/vulnerability management; logging/detection/response
   readiness. Explain relationships before individual controls; preserve
   control values as evidence.
4. **Professional and client-consultative standards.** Third-person formal
   consultative voice; consistent tense; concise explanatory prose (expand
   only where the draft under-explains); declarative cadence; precise
   security terminology; claims are stated directly when supported and
   hedged only when uncertainty changes the conclusion; findings as
   condition → evidence → implication; recommendations as action →
   rationale; banned: first-person, contractions, colloquialisms, filler,
   marketing language, unqualified superlatives. The document must read as
   one coherent quarter-over-quarter narrative — transitions and
   cross-section context built ONLY from facts in the draft. Break up
   Microsoft-style noun stacks and product-vendor grammar; write normal
   English sentences with clear subjects and verbs (e.g., "positive
   reported completed controls" becomes "Windows antivirus and EDR is in
   Block mode"). Correct spelling, grammar, and punctuation; use
   inclusive and neutral language; avoid sensitive geopolitical references
   or contentious framing. Every sentence has a clear subject and verb and
   states one idea; semicolons connect only closely related clauses;
   evidence gaps are integrated as supporting context, not left as raw
   fragments; scope is stated in plain language; every substantive finding
   or risk is paired with a concrete client action.
   **Client-consultative writing standard:** this is a consultant's
   security assessment written for a client stakeholder. Lead with the
   supported security conclusion, then the evidence, then why it matters,
   then the practical next step. Do not lead with methodology, scoring
   mechanics, evidence limitations, or framework mappings unless they
   materially determine the conclusion. Sound direct, calm, practical, and
   professionally opinionated where the evidence supports a judgment.
   Interpret technical findings rather than restating them; Microsoft
   recommendations, Secure Score, CIS, and NIST are supporting inputs, not
   the narrative voice. Tables may carry detailed control evidence and
   framework mappings; prose explains posture, significance, priorities,
   and next steps without duplicating table contents. Every substantive
   domain should leave the reader knowing: (1) the consultant's assessment,
   (2) what meaningfully changed, if anything, (3) why it matters, and (4)
   what to focus on next. Plain language must never become unsupported
   certainty — preserve the draft's factual boundary and all material
   distinctions.
5. **Missing content = punch-out, never fabrication.** If the template
   requires a section `draft.md` has no content for, write
   `punchout-missing-content.md`, halt, and ask the human to decide:
   provide content / drop section / defer.
6. **SUBSTANCE FREEZE — hard constraint.** Every number, date, percentage,
   finding title, CIS/NIST reference, classification outcome, measurement,
   recommendation, piece of evidence, and client-facing meaning must
   remain identical to `draft.md`. Evidence is quoted or faithfully
   restated, never re-written or re-scoped. Analysis may add
   interpretation and context but no fact absent from the draft.
7. **Table review.** Inventory every table in `draft.md`; review each
   row. Narrative cells (captions, row labels, interpretation columns) must
   have clear subjects and verbs; verbless fragments, raw evidence-gap
   labels, and semicolon-joined unrelated claims are rewritten as complete
   consultative sentences. Reference/metric shorthand (CIS IDs, scores,
   counts) is preserved. Table structure, rows, and columns are frozen.
   Record any table-prose mismatch, total-reconciliation gap, or template
   deviation as an observation; do not correct draft errors.
8. **Log every edit** in `tone-changes.json`. Each prose edit and each
   edited table row gets one record with: `location`, `before`, `after`,
   `style_marker`, `reason`, `assessment_function`, and `facts_changed`
   (must be `false`). Add `tables_reviewed` (one entry per table) and
   `observations` (detected draft issues for human review) as top-level
   arrays. Validate against `schemas/tone-changes.schema.json`.

## Step 06 — Adversarial Review & Finalize (run only after 05 completes)

**G5 — four checks, all must pass:**

1. **Assessment quality:** `toned.md` conforms to the template; every
   section is conclusion-first with the applicable elements above
   (except any section titled "Security Awareness" case-insensitively,
   which is exempt from the conclusion-first/synthesis requirement but
   still subject to the substance freeze and banned-pattern checks);
   domains carry an explicit quarter-over-quarter thesis with correct
   attribution; controls are synthesized as themes; professional
   standards met; significance is tied to the client's business risk
   ("so what?"); movement claims are temporally anchored; positive findings
   are calibrated with their limitations and risks with their known/unknown
   scope; no noun stacks or product-vendor grammar remain; spelling,
   grammar, and punctuation are correct; language is inclusive and neutral;
   no sensitive geopolitical references; every sentence has a clear
   subject and verb and states one idea, including inside table narrative
   cells; semicolons connect only closely related clauses; evidence gaps
   are integrated as supporting context; scope is stated in plain language;
   every substantive finding or risk is paired with a concrete client
   action; the document reads as consultant guidance rather than audit/Secure
   Score commentary; technical findings are interpreted for the client;
   concise consultative prose without defensive caveat-stacking; table
   captions, row labels, and narrative cells are reviewed for banned
   patterns, vague temporal terms, and sentence discipline; every draft
   table is listed in `tone-changes.json#tables_reviewed`. A data-first,
   unchanged, word-patched, or table-unreviewed `toned.md` fails even with
   zero banned patterns.
2. **Substance freeze:** every factual token in `toned.md` exists in
   `draft.md` unchanged, including those inside tables; no re-written/re-scoped
   evidence; no invented facts in narrative framing; no shifted table-cell
   meaning; no meaning-shifting paraphrase.
3. **Change accountability:** `tone-changes.json` is complete,
   schema-valid, and contains both `tables_reviewed` and `observations`;
   every draft→toned edit is recorded, with table edits verified row by row;
   all records have `facts_changed: false`; every draft table appears in
   `tables_reviewed`; the `assessment_function` coverage demonstrates
   analytical synthesis (a log of only `template_structure`/
   `professional_tone` fails — it means no real rewrite happened).
4. **Punch-out closure:** no unresolved `punchout-missing-content.md`.

**On any G5 failure:** state `G5 failure` and the specific check. Before looping back or escalating, append a failure record to `audit/success-log.md` and a final step record to `audit/run-YYYYMMDD.jsonl` with `guardrail_inbound: {"id": "G5", "result": "fail"}` and `finalized: false`. Then loop back to Step 05 once. If G5 fails twice, escalate to the human.

**On G5 pass:**
- Assemble `toned.md` into the final deliverable using the template's
  delivery format (file type, naming, front matter).
- Write `audit-report.md`: steps executed, G4/G5 results, punch-outs
  raised/resolved, tone-change statistics by `style_marker` and
  `assessment_function`, and end-to-end outcome.
- Append to `success-log.md`: `| date | run id | result | punch-outs | iterations | notes |`.
- Append step records to `run-YYYYMMDD.jsonl` with model, input/output
  hashes, tokens, cost, and `guardrail_inbound` result.

**Logging is mandatory.** A run is not complete until both `audit/success-log.md` and `audit/run-YYYYMMDD.jsonl` have been updated. If the logging step is missed, fail the run and report which log is missing.

## Reporting contract

1. Before editing, list every workflow file you opened and its role —
   including whether you used this orchestrator or the SKILL.md files.
2. Show each G4/G5 check result explicitly. Before finalizing, report the G5 adversarial review result and which specific check passed or failed.
3. Output `tone-changes.json` in full.
4. If a punch-out triggers, stop and show its content for human decision.
5. In the final output, explain the workflow used and what each guardrail
   verified.
