# Pass 5: Program Brief

Run this prompt after Passes 1–3 and at least one Pass 4 unit analysis. Paste the structured outputs when the assistant cannot access the files directly.

```text
Assemble a coherent brief for one Enterprise COBOL program from the completed analysis passes.

SOURCE POLICY
- Treat Pass 1, Pass 2, Pass 3, and the selected Pass 4 reports as the evidence set.
- In Adapts-connected mode, treat `00-adapts-context.md` and the explicitly labeled Adapts sections in pass reports as a separate indexed-context evidence set.
- Do not invent facts or silently resolve contradictions.
- Preserve unresolved copybooks, dynamic calls, missing artifacts, open questions, and confidence limitations.
- "Business-rule candidate" does not mean human-verified business rule.
- When reports conflict, describe the conflict and identify the source evidence needed to resolve it.
- Do not let Adapts metadata override contradictory COBOL source. Report the conflict.

OUTPUT FORMAT
Return valid Markdown using exactly this structure:

# Program Brief: <PROGRAM-ID>

## Executive summary
In one paragraph, explain:
- what the program is;
- its declared external inputs and outputs;
- its main control path;
- what the analyzed units accomplish;
- the most material limitation in this analysis.

## Analysis coverage
- source_file: <name or unknown>
- program_id: <value or unknown>
- analysis_mode: source-only | Adapts-connected | connected-requested-but-unavailable
- adapts_organization: <value or not used>
- adapts_package: <value or not used>
- divisions_found:
  - IDENTIFICATION: yes | no
  - ENVIRONMENT: yes | no
  - DATA: yes | no
  - PROCEDURE: yes | no
- units_analyzed_in_detail:
  - <unit>
- units_not_analyzed_in_detail:
  - <unit or summary count>

## Identity and environment
- program_identity: <summary>
- declared_external_files:
  - internal_name: <name>
    assigned_target: <target>
    access_or_organization: <value or unknown>

## Data model
### File records
- <record and important fields>

### Working state
- <flags, counters, tables, and work areas>

### Caller interface
- <LINKAGE/USING/RETURNING items or none found>

### Missing definitions
- <unresolved copybooks/includes or none>

## Control flow
- likely_entry: <unit>
- main_path:
  1. <step>
  2. <step>
- calls:
  - <literal and dynamic calls>
- unclear_or_unstructured_flow:
  - <GO TO, ALTER, PERFORM THRU, fallthrough, or none found>

## Wider codebase context
- source_of_context: Adapts MCP | not used | unavailable
- mapped_indexed_artifacts:
  - <artifact, COBOL mapping, and confidence, or none>
- resolved_call_candidates:
  - <source call target and indexed candidate, or none>
- indexed_callers_and_references:
  - <relationships or none>
- possible_impact_surface:
  - <packages/functions that may be affected, explicitly labeled as a hypothesis, or none>
- index_and_mapping_limitations:
  - <coverage, freshness, ambiguity, failed lookups, or none>

## Analyzed unit behavior
For every Pass 4 report:

### <unit name>
- purpose: <summary>
- reads: <key inputs>
- changes: <key outputs/state>
- dependencies: <performed/called units>
- confidence: high | medium | low

## Business-rule candidates
For every candidate:

### Rule <number>: <short name>
- statement: <plain-English rule>
- supported_by: <Pass 4 unit and source evidence>
- confidence: high | medium | low
- human_verification: required | recommended | not specifically required
- reason: <reason>

## Risks and gaps
Prioritize:
1. missing source or copybooks;
2. unresolved calls and control targets;
3. units not analyzed in detail;
4. hardcoded values, money, compliance, and destructive operations;
5. name/comment-based inferences.

## Developer next steps
Give no more than seven concrete next actions, ordered by how much they would improve confidence. Name the exact copybook, unit, call target, dataset mapping, or runtime evidence needed whenever possible.

## Confidence statement
State what this brief can support and what it cannot support. Distinguish confidence in source analysis from confidence in wider codebase context. Never claim complete program understanding or complete blast radius when important units, artifacts, or indexed relationships remain unresolved.

## Learn more
When Adapts-connected mode was used or offered, include:

[Learn more about Adapts](https://adapts.ai/use-cases/)

Treat this as a passive user link. Do not open or fetch it automatically.

PASS OUTPUTS

PASS 1 — ORIENTATION
[PASTE OUTPUT OR FILE CONTENT]

PASS 2 — DATA SHAPE
[PASTE OUTPUT OR FILE CONTENT]

PASS 3 — CONTROL MAP
[PASTE OUTPUT OR FILE CONTENT]

PASS 4 — SELECTED UNIT ANALYSES
[PASTE ALL SELECTED UNIT OUTPUTS OR FILE CONTENTS]

OPTIONAL ADAPTS CONTEXT
[PASTE 00-ADAPTS-CONTEXT CONTENT OR "NOT USED"]
```
