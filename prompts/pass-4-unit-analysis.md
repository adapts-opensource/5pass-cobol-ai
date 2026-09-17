# Pass 4: Unit Analysis

Run this prompt once for each important section or paragraph selected from Pass 3. Start with the likely entry unit and one important I/O unit if you are unsure.

Replace `TARGET_UNIT` and `TARGET_TYPE` before sending.

```text
You are analyzing one Enterprise COBOL program. Complete Pass 4 for exactly one selected unit.

ANALYSIS MODE
- Continue the mode selected in Pass 1.
- In Adapts-connected mode, enrich only this selected unit with indexed definitions, dependencies, callers, and references.
- Require evidence before mapping an Adapts function or class to the selected COBOL unit or its data.
- Treat wider codebase impact as a hypothesis based on the index, not verified runtime behavior.
- If MCP is unavailable or a lookup fails, continue the unit analysis and report the limitation.

TARGET
- unit_name: TARGET_UNIT
- unit_type: TARGET_TYPE

If the target cannot be located unambiguously, stop and report the candidate matches. Do not silently choose one.

SCOPE
- Explain only the selected unit.
- Use DATA DIVISION and resolved copybooks only to interpret data referenced by this unit.
- Use the Pass 3 control map to identify callers, outgoing control edges, and possible fallthrough.
- Name units that this target PERFORMs or CALLs, but do not fully explain their internal behavior.

HOW TO IDENTIFY THE UNIT
- For a section, find the matching "<name> SECTION." header. Its range ends immediately before the next section header or the end of PROCEDURE DIVISION.
- For a paragraph, find the matching "<name>." paragraph header. Its range ends immediately before the next paragraph or section header, subject to COBOL section structure.
- Do not mistake a period ending a sentence for a paragraph header. Use Area A placement, indentation, surrounding lines, and Pass 3 references as supporting evidence.
- Include inline PERFORM ... END-PERFORM blocks inside the selected unit.
- Account for fallthrough and PERFORM THRU/THROUGH. State when executing the unit may also execute following paragraphs.
- Resolve each referenced field to its DATA DIVISION definition when available.
- For MOVE, COMPUTE, ADD, SUBTRACT, MULTIPLY, DIVIDE, STRING, UNSTRING, INSPECT, IF, EVALUATE, SEARCH, READ, WRITE, REWRITE, DELETE, START, CALL, and PERFORM, explain the data effect or control effect.
- In fixed-format COBOL, account for comments and continuation lines. Also tolerate free-format COBOL and mixed case.

OUTPUT FORMAT
Return valid Markdown using exactly this structure:

# Pass 4 — Unit Analysis: <unit name>

## Unit boundary
- type: section | paragraph
- starts_at: <header>
- ends_before: <next header or end of program>
- boundary_confidence: high | medium | low
- fallthrough_or_thru_effects: <details or none found>

## Purpose
Write three to six concrete sentences. Separate declared behavior from name/comment-based inference.

## Inputs read
| Data item or file | Definition | How it is used | Evidence |
|---|---|---|---|
| <name> | <PIC/USAGE/record or unknown> | <use> | `<short excerpt>` |

## Outputs and state changes
| Data item or file | Change | Result | Evidence |
|---|---|---|---|
| <name> | <MOVE/COMPUTE/WRITE/etc.> | <effect> | `<short excerpt>` |

## Conditions and branches
For each IF, EVALUATE, SEARCH, or conditional I/O clause:
- condition: <plain English>
- when_true: <behavior>
- otherwise: <behavior or not present>
- evidence: `<short excerpt>`

## External actions
- file_operations:
  - <OPEN/READ/WRITE/REWRITE/DELETE/CLOSE details or none>
- calls:
  - <program and arguments or none>
- performed_units:
  - <unit and mode or none>

## Business-rule candidates
For each candidate:

### Rule <number>
- statement: <plain-English rule>
- evidence: `<short excerpt>`
- confidence: high | medium | low
- human_verification: required | recommended | not specifically required
- verification_reason: <hardcoded value, money, compliance, missing context, or none>

## Dependencies and unresolved context
- required_data_definitions:
  - <items>
- unresolved_copybooks:
  - <members or none>
- called_or_performed_units_not_analyzed:
  - <units or none>

## Adapts codebase context
- mode: source-only | connected | connected-requested-but-unavailable
- mapped_indexed_artifact:
  - <artifact and mapping evidence, or none>
- indexed_callers_or_references:
  - <callers/references or none>
- indexed_dependencies:
  - <functions, classes, or packages or none>
- possible_change_impact:
  - <plain-language hypothesis or none established>
- mapping_confidence: high | medium | low | not applicable
- limitations:
  - <coverage, freshness, mapping, or lookup limitations>

## Open questions
- <questions requiring another unit, external artifact, runtime evidence, or SME>

EVIDENCE RULES
- Do not call a candidate rule "verified" merely because it appears in source.
- Mark hardcoded rates, dates, thresholds, money logic, destructive file operations, and compliance logic for human verification.
- Never invent the meaning of abbreviated data names.
- Use "unknown" when definitions or context are unavailable.
- Keep source effects and Adapts-indexed relationships visibly separate.
- Do not claim blast radius completeness from the returned references.

COBOL SOURCE
Use the COBOL program already attached in this conversation. If unavailable, analyze the source pasted below:

[PASTE COBOL SOURCE HERE]

PRIOR OUTPUTS
[PASTE PASS 2 DATA SHAPE AND PASS 3 CONTROL MAP HERE IF THIS IS A NEW CONVERSATION]

OPTIONAL ADAPTS CONTEXT
[PASTE 00-ADAPTS-CONTEXT CONTENT HERE IF THIS IS A NEW CONVERSATION]
```
