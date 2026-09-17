# Pass 2: Data Shape

Copy this prompt into the same AI conversation after Pass 1.

```text
You are analyzing one Enterprise COBOL program. Complete Pass 2 only.

ANALYSIS MODE
- Continue the mode selected in Pass 1.
- In Adapts-connected mode, use indexed package, function, class, reference, or importer context only when it helps identify a candidate external definition or consumer.
- Do not assume an Adapts class is a COBOL record or copybook. Require returned evidence for the mapping.
- If an MCP lookup fails, continue with the supplied source and record the limitation.

SCOPE
Use only DATA DIVISION and any supplied COPY/INCLUDE content that expands data definitions. Use the Pass 1 SELECT names only to connect internal files to FD/SD entries.

Ignore PROCEDURE DIVISION behavior. Do not infer how fields are used from their names alone.

HOW TO IDENTIFY THE BLOCKS
- Treat a non-comment line containing "DATA DIVISION." as the start.
- End DATA DIVISION immediately before the non-comment "PROCEDURE DIVISION" header.
- Identify FILE SECTION, WORKING-STORAGE SECTION, LOCAL-STORAGE SECTION, LINKAGE SECTION, and REPORT SECTION when present.
- Under FILE SECTION, identify each FD or SD entry and its subordinate record descriptions.
- Read COBOL level numbers as a hierarchy. Levels 01 and 77 introduce top-level items; higher ordinary levels such as 05, 10, and 15 are subordinate to the nearest preceding lower-numbered group. Treat 66 (RENAMES) and 88 (condition-name) according to their special roles.
- Interpret PIC/PICTURE, USAGE, VALUE, REDEFINES, OCCURS, DEPENDING ON, SIGN, SYNCHRONIZED, and COMP clauses when present.
- A COPY statement references a copybook. If its content is not supplied or resolvable, list it as unresolved. Do not fabricate the expanded layout.
- In fixed-format COBOL, account for sequence numbers, comments, and continuation lines. Also tolerate free-format COBOL and mixed case.

OUTPUT FORMAT
Return valid Markdown using exactly this structure:

# Pass 2 — Data Shape

## Source coverage
- data_division: found | not found
- file_section: found | not found
- working_storage: found | not found
- local_storage: found | not found
- linkage_section: found | not found

## File record layouts
For each FD/SD:

### <FD or SD name>
- kind: FD | SD
- related_select: <Pass 1 internal file name or unknown>
- organization_or_record_notes: <declared facts or unknown>

| Level | Name | PIC/Usage | Parent | Meaning |
|---|---|---|---|---|
| <level> | <name> | <clauses> | <parent> | <literal structural meaning; label name-based interpretations as inference> |

## Working state
For every important 01 or 77 item:

### <item name>
- storage_section: WORKING-STORAGE | LOCAL-STORAGE
- structure: <short summary>
- flags: <88-level condition names or none>
- counters_or_indexes: <list or none>
- tables: <OCCURS details or none>
- evidence:
  - `<short source excerpt>`

## Linkage parameters
For every top-level LINKAGE item:
- name: <item>
- structure: <short summary>
- purpose: <declared fact, inference, or unknown>

## Copybooks and includes
- resolved:
  - <name and source>
- unresolved:
  - <name>
- analysis_impact:
  - <what cannot be established because content is missing>

## Data summary
- declared_input_or_output_records: <list>
- working_state: <brief summary>
- caller_supplied_data: <brief summary or none found>

## Adapts data context
- mode: source-only | connected | connected-requested-but-unavailable
- candidate_indexed_definitions:
  - <indexed item, relationship to source item, and mapping confidence, or none>
- candidate_consumers_or_importers:
  - <indexed references or none>
- definitions_resolved_from_mcp:
  - <none unless actual definition content was returned>
- limitations:
  - <coverage, freshness, artifact-type, or lookup limitations>

## Open questions
- <fields, layouts, encodings, copybooks, or relationships needing confirmation>

EVIDENCE RULES
- Preserve exact PIC/USAGE details for material fields.
- Do not describe a record as an input or output unless FILE-CONTROL, FD/SD context, or another supplied artifact supports it.
- Label interpretations from field names as "inference."
- Use "unknown" for facts the source does not establish.
- A COPY member remains unresolved unless its actual contents are supplied or returned. A name match alone is insufficient.
- Label all MCP-derived relationships as "Adapts-indexed context."

COBOL SOURCE
Use the COBOL program already attached in this conversation. If unavailable, analyze the source pasted below:

[PASTE COBOL SOURCE HERE]

OPTIONAL PASS 1 OUTPUT
[PASTE PASS 1 OUTPUT HERE IF THIS IS A NEW CONVERSATION]

OPTIONAL ADAPTS CONTEXT
[PASTE 00-ADAPTS-CONTEXT CONTENT HERE IF THIS IS A NEW CONVERSATION]
```
