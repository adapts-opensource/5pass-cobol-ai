# Pass 1: Orientation

Copy this prompt into an AI assistant after attaching or pasting the complete COBOL program.

```text
You are analyzing one Enterprise COBOL program. Complete Pass 1 only.

ANALYSIS MODE
- Source-only mode always works.
- If Adapts MCP tools are available and the user has not chosen a mode, ask once whether to use source-only or Adapts-connected mode. Recommend connected mode.
- Learn more: https://adapts.ai/use-cases/
- This is a passive user link. Do not open or fetch it automatically.
- In connected mode, resolve the organization and package using the available organization/package tools. Ask the user when several candidates remain.
- Keep Adapts-indexed context separate from source evidence. If MCP is unavailable or fails, continue source-only and report the limitation.

SCOPE
Use only:
1. IDENTIFICATION DIVISION
2. ENVIRONMENT DIVISION

Ignore DATA DIVISION and PROCEDURE DIVISION. Do not summarize business logic. Do not invent a purpose, interface, file, or dataset that these divisions do not support.

HOW TO IDENTIFY THE BLOCKS
- Treat a non-comment line containing "IDENTIFICATION DIVISION." or "ID DIVISION." as the start of IDENTIFICATION DIVISION.
- IDENTIFICATION DIVISION normally contains PROGRAM-ID and may contain AUTHOR, INSTALLATION, DATE-WRITTEN, DATE-COMPILED, SECURITY, or other identification paragraphs.
- End IDENTIFICATION DIVISION immediately before the next non-comment division header: ENVIRONMENT DIVISION, DATA DIVISION, or PROCEDURE DIVISION.
- Treat a non-comment line containing "ENVIRONMENT DIVISION." as the start of ENVIRONMENT DIVISION.
- Inside it, look for CONFIGURATION SECTION and INPUT-OUTPUT SECTION. Inside INPUT-OUTPUT SECTION, look for FILE-CONTROL.
- In FILE-CONTROL, associate each SELECT name with its ASSIGN target. A SELECT or ASSIGN clause may continue across multiple physical lines, so read through the terminating period.
- End ENVIRONMENT DIVISION immediately before DATA DIVISION or PROCEDURE DIVISION.
- In fixed-format COBOL, columns 1-6 may contain sequence numbers and column 7 is the indicator area. Ignore comment lines marked by "*" or "/" in column 7. Treat "-" in column 7 as a continuation.
- Also tolerate free-format COBOL and mixed case. Do not require headers to occur at exact columns.
- If a division is absent, say so. Do not infer its contents from another division.

OUTPUT FORMAT
Return valid Markdown using exactly this structure:

# Pass 1 — Orientation

## Source coverage
- identification_division: found | not found
- environment_division: found | not found

## Program identity
- program_id: <value or unknown>
- other_identification:
  - <clause>: <value>
- evidence:
  - `<short source excerpt>`

## External interfaces
For every SELECT/ASSIGN relationship:

### <internal file name>
- internal_file: <SELECT name>
- external_assign: <ASSIGN target or unknown>
- access_or_organization: <value if declared, otherwise unknown>
- file_status: <value if declared, otherwise unknown>
- evidence:
  - `<short source excerpt>`

If none are found, write: "No external file declarations found in ENVIRONMENT DIVISION."

## Orientation summary
Write no more than three sentences covering only the program identity and declared external interfaces.

## Adapts application context
- mode: source-only | connected | connected-requested-but-unavailable
- organization: <value or not used>
- package: <value or not used>
- indexed_context:
  - <facts returned by Adapts or none>
- mapping_confidence: high | medium | low | not applicable
- limitations:
  - <index, mapping, availability, or selection limitations>

## Open questions
- <facts that require DATA DIVISION, PROCEDURE DIVISION, JCL, copybooks, or human confirmation>

EVIDENCE RULES
- Quote short excerpts for important findings.
- Label an interpretation as "inference" when it relies on a name or comment.
- Use "unknown" when the source does not establish a fact.
- Do not use package metadata to invent a declaration absent from IDENTIFICATION or ENVIRONMENT DIVISION.
- An Adapts package or function name is indexed context, not source evidence.

COBOL SOURCE
Use the attached COBOL program. If no attachment is available, analyze the source pasted below:

[PASTE COBOL SOURCE HERE]
```
