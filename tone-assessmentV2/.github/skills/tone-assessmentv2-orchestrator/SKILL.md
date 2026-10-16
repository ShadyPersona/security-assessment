---
name: tone-assessmentv2-orchestrator
description: Authoritative instructions for running the tone-assessmentV2 workflow (steps 05 + 06) — security-assessment synthesis of one draft document against a template, under a substance freeze, with guardrails G4/G5, punch-outs, and a full audit trail.
---

# tone-assessmentV2 — Orchestrator Instructions

You are running a two-step security-assessment synthesis workflow. This file
is the authoritative definition. If companion files exist
(`WORKFLOW.md`, `.github/skills/05-tone-consistency/SKILL.md`,
`.github/skills/06-finalize/SKILL.md`, `schemas/tone-changes.schema.json`,
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
4. **Professional standards.** Third-person formal consultative voice;
   consistent tense; verbose explanatory prose (no bare metrics where the
   draft provides context); declarative cadence; precise security
   terminology; hedged/evidence-attributed claims; findings as
   condition → evidence → implication; recommendations as
   action → rationale; banned: first-person, contractions, colloquialisms,
   filler, marketing language, unqualified superlatives. The document must
   read as one coherent quarter-over-quarter narrative — transitions and
   cross-section context built ONLY from facts in the draft. Break up
   Microsoft-style noun stacks and product-vendor grammar; write normal
   English sentences with clear subjects and verbs (e.g., "positive
   reported completed controls" becomes "Windows antivirus and EDR is in
   Block mode"). Correct spelling, grammar, and punctuation; use
   inclusive and neutral language; avoid sensitive geopolitical references
   or contentious framing.
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
7. **Log every edit** in `tone-changes.json`. Each record requires:
   `location`, `before`, `after`, `style_marker`, `reason` (what the edit
   accomplished analytically), `assessment_function` (one of:
   `executive_conclusion`, `quarter_movement`, `cross_signal_synthesis`,
   `evidence_interpretation`, `security_significance`,
   `evidence_limitation`, `recommendation_traceability`,
   `historical_context`, `template_structure`, `professional_tone`), and
   `facts_changed` (must be `false` — `true` is a hard fail).

## Step 06 — Finalize (run only after 05 completes)

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
   no sensitive geopolitical references. A data-first, unchanged, or
   word-patched `toned.md` fails even with zero banned patterns.
2. **Substance freeze:** every factual token in `toned.md` exists in
   `draft.md` unchanged; no re-written/re-scoped evidence; no invented
   facts in narrative framing; no meaning-shifting paraphrase.
3. **Change accountability:** `tone-changes.json` is complete and
   schema-valid; every draft→toned edit is recorded; all records have
   `facts_changed: false`; the `assessment_function` coverage demonstrates
   analytical synthesis (a log of only `template_structure`/
   `professional_tone` fails — it means no real rewrite happened).
4. **Punch-out closure:** no unresolved `punchout-missing-content.md`.

**On any G5 failure:** state `G5 failure` and the specific check, loop
back to Step 05 once, and re-run. If G5 fails twice, escalate to the human.

**On G5 pass:**
- Assemble `toned.md` into the final deliverable using the template's
  delivery format (file type, naming, front matter).
- Write `audit-report.md`: steps executed, G4/G5 results, punch-outs
  raised/resolved, tone-change statistics by `style_marker` and
  `assessment_function`, and end-to-end outcome.
- Append to `success-log.md`: `| date | run id | result | punch-outs | iterations | notes |`.
- Append step records to `run-YYYYMMDD.jsonl` with model, input/output
  hashes, tokens, cost, and `guardrail_inbound` result.

## Reporting contract

1. Before editing, list every workflow file you opened and its role —
   including whether you used this orchestrator or the SKILL.md files.
2. Show each G4/G5 check result explicitly.
3. Output `tone-changes.json` in full.
4. If a punch-out triggers, stop and show its content for human decision.
5. In the final output, explain the workflow used and what each guardrail
   verified.
