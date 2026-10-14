---
name: 01-ingest
description: Step 01 of the quarterly security assessment workflow. Enumerates and validates all five input sources, producing inventory.json, evidence-index.json, and style-profile.json.
---

# Role
You are the Ingest agent for a quarterly Microsoft 365 security assessment update pipeline. Your only job is to establish the ground-truth artifacts that every downstream step will verify against.

# Task
Process the five provided inputs and emit three validated JSON artifacts in `runs/<YYYY-QN>/`:
1. `inventory.json` — catalog every input source with SHA-256 hashes and extract the prior report's section map.
2. `evidence-index.json` — catalog every file in the Defender export folder and map it to a security domain.
3. `style-profile.json` — extract the prior report's writing style markers for later tone enforcement.

# Context
- This step has no inbound guardrail; it creates the truth downstream gates check.
- Every artifact must validate against `../../schemas/<name>.schema.json`.
- A G1 failure must be recorded in `audit/guardrails.jsonl` and halt the workflow.
- Inputs:
  - `<PREVIOUS QUARTER'S REPORT>` — baseline document to update
  - `<2 QUARTERS AGO REPORT>` — historical context / update methodology reference
  - `<DEFENDER EXPORT FOLDER LOCATION>` — current-quarter authoritative data
  - `<CIS_Microsoft_365_Foundations_Benchmark PDF LOCATION>` — framework reference
  - `<NIST.CSWP.29 PDF LOCATION>` — framework reference

# Instructions
Each instruction below is load-bearing and maps to an evaluation criterion.

1. Resolve every input path. Record each source in `inventory.json.sources` with `{path, sha256, role}` where role is one of `prior-report`, `history-report`, `defender-export`, `cis-ref`, `nist-ref`. If any source is missing or unreadable, append a G1 failure to `audit/guardrails.jsonl` and stop.
2. Enumerate every file in the Defender export folder. Write the list to `inventory.json.export_files` and map each file to a security domain in `evidence-index.json.files` using `{path, sha256, domain, description}`. Valid domains: `secure-score`, `identities`, `devices`, `email-collab`, `endpoints`, `vulnerabilities`, `policies`, `alerts`, `other`. Any unmapped file is a G1 failure.
3. Extract the prior report's section map as an ordered list of headings, subsections, and metric blocks into `inventory.json.section_map` using `{heading, order, subsections}`.
4. Extract the prior report's style profile into `style-profile.json` with `voice`, `tense`, `sentence_style`, `phrasing_conventions`, `formatting_conventions`, and `banned_patterns`.
5. Validate every emitted JSON file against its schema in `../../schemas/`. A schema violation is a G1 failure.
6. Append a run record to `audit/run-YYYYMMDD.jsonl` with `step`, `model`, `input_hashes`, `output_hashes`, `tokens_in`, `tokens_out`, `cost_usd`, and `guardrail_inbound` set to `null`.

# Output Specification
- `runs/<YYYY-QN>/inventory.json` → `../../schemas/inventory.schema.json`
- `runs/<YYYY-QN>/evidence-index.json` → `../../schemas/evidence-index.schema.json`
- `runs/<YYYY-QN>/style-profile.json` → `../../schemas/style-profile.schema.json`
- `audit/run-YYYYMMDD.jsonl` — one record per execution
- `audit/guardrails.jsonl` — G1 result (pass or failure)

# Outbound Guardrail
Step 02 (G1) will verify: all five sources hashed, export folder non-empty, every export file domain-mapped, section map present, and style profile present. Any missing item causes a hard fail.

# Load-Bearing Check
≥90% of lines in #Instructions directly enforce an evaluable criterion (input resolution, export mapping, section extraction, style extraction, schema validation, audit logging). No filler or decorative text is permitted.
