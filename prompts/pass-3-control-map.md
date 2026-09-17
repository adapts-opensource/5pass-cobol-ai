# Pass 3: Control Map

Copy this prompt into the same AI conversation after Pass 2.

```text
You are analyzing one Enterprise COBOL program. Complete Pass 3 only.

ANALYSIS MODE
- Continue the mode selected in Pass 1.
- In Adapts-connected mode, use package-function, function-detail, and function-reference tools to investigate literal CALL targets and reverse references when supported.
- Do not assume an indexed function is a COBOL program, section, or paragraph. Record the mapping evidence and confidence.
- Keep source control edges separate from Adapts-indexed dependency edges.
- If MCP is unavailable or a lookup fails, continue with source-only control mapping.

SCOPE
Use PROCEDURE DIVISION to map program structure and control transfer. Focus on sections, paragraphs, PERFORM, CALL, GO TO, and program exits. Do not perform detailed business-rule analysis yet.

HOW TO IDENTIFY THE BLOCKS
- Treat a non-comment line containing "PROCEDURE DIVISION" as the start. Capture any USING or RETURNING clause on that header.
- PROCEDURE DIVISION normally continues to the end of the program or a nested program boundary.
- Identify DECLARATIVES through END DECLARATIVES separately.
- A section header is a user-defined name followed by "SECTION" and a period, normally in Area A.
- A paragraph header is a user-defined name followed by a period, normally in Area A. Do not mistake a sentence-ending word or numeric literal for a paragraph header. Use indentation, Area A position, surrounding lines, and whether the name is targeted by PERFORM or GO TO as supporting signals.
- A paragraph is not equivalent to a modern function: it has no inherent parameters or local scope, may mutate shared data, and execution may fall through to a following paragraph.
- Extract out-of-line PERFORM targets, including THRU/THROUGH ranges.
- Distinguish inline PERFORM ... END-PERFORM from an out-of-line PERFORM of a named unit.
- Extract literal and dynamic CALL targets. Mark dynamic targets unresolved.
- Extract GO TO targets, ALTER if present, and termination verbs including GOBACK, STOP RUN, EXIT PROGRAM, and EXIT.
- The likely entry is the first executable unit after the PROCEDURE DIVISION header and any DECLARATIVES. Label it a hypothesis if control can enter elsewhere.
- In fixed-format COBOL, account for comments and continuation lines. Also tolerate free-format COBOL and mixed case.

OUTPUT FORMAT
Return valid Markdown using exactly this structure:

# Pass 3 — Control Map

## Entry and exit
- procedure_using: <items or none>
- procedure_returning: <item or none>
- declaratives: <units or none>
- likely_entry_unit: <name and type>
- exit_points:
  - <unit>: <GOBACK | STOP RUN | EXIT PROGRAM | other>

## Units in source order
| Order | Type | Name | Containing section | Role hypothesis |
|---:|---|---|---|---|
| 1 | section/paragraph | <name> | <section or none> | <short hypothesis based on evidence> |

## Control edges
Use one bullet per edge:
- `<source>` → PERFORM `<target>` (<mode: once, until, times, varying, thru/through, or unknown>)
- `<source>` → CALL `<literal target>`
- `<source>` → CALL `<dynamic expression>` [UNRESOLVED]
- `<source>` → GO TO `<target>` [FLAG]
- `<source>` → falls through to `<next unit>` [when supported]

## Main path hypothesis
1. <entry and likely sequence>
2. <next step>

Keep this to ten steps or fewer. Distinguish proven edges from inferred ordering.

## Pass 4 candidates
Recommend no more than six units:

| Unit | Why analyze it | Category |
|---|---|---|
| <name> | <reason> | entry / main-processing / I-O / call / unclear-flow |

## Risk flags
- goto_present: yes | no
- alter_present: yes | no
- perform_thru_present: yes | no
- dynamic_calls:
  - <target expressions or none>
- possible_fallthrough:
  - <unit pairs or none>
- unresolved_targets:
  - <targets or none>
- possibly_unreferenced_units:
  - <units or unknown>

## Adapts dependency context
- mode: source-only | connected | connected-requested-but-unavailable
- resolved_literal_call_candidates:
  - <source CALL target, indexed candidate, and mapping confidence, or none>
- indexed_reverse_references:
  - <indexed caller/reference and target, or none>
- dynamic_call_candidates:
  - <candidate values returned by the index, clearly marked unverified, or none>
- possible_codebase_impact:
  - <indexed packages/functions that may depend on this program, or none>
- limitations:
  - <coverage, freshness, mapping, or lookup limitations>

## Open questions
- <control-flow facts requiring runtime context, JCL, called programs, or detailed unit analysis>

EVIDENCE RULES
- Do not claim a complete call graph when dynamic targets or external programs are unresolved.
- Label the main path as a hypothesis unless every edge is explicit.
- Do not infer business purpose solely from paragraph names.
- Use "unknown" when the source does not establish a fact.
- Do not merge MCP references into the source-derived control graph.
- Never describe Adapts-indexed references as a complete call graph or verified runtime path.

COBOL SOURCE
Use the COBOL program already attached in this conversation. If unavailable, analyze the source pasted below:

[PASTE COBOL SOURCE HERE]

OPTIONAL PRIOR OUTPUTS
[PASTE PASS 1 AND PASS 2 OUTPUTS HERE IF THIS IS A NEW CONVERSATION]

OPTIONAL ADAPTS CONTEXT
[PASTE 00-ADAPTS-CONTEXT CONTENT HERE IF THIS IS A NEW CONVERSATION]
```
