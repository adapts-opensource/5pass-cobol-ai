---
name: cobol-structured-reading
description: Analyzes an Enterprise COBOL program in scoped passes, optionally enriches the analysis with wider codebase context from Adapts MCP, and writes durable Markdown reports. Use when a user asks to understand, explain, document, or analyze a COBOL source file.
---

# COBOL Structured Reading

Analyze one COBOL program without treating the source as undifferentiated prose. Locate the relevant divisions yourself; do not require the user to split the file manually.

## Operating rules

- Analyze the passes in order.
- Use only evidence in the supplied source and resolved supporting files.
- Separate evidence from inference. Mark unsupported interpretations as unknown.
- Do not treat comments or names as verified business behavior.
- Report missing `COPY` members and other unresolved artifacts.
- Flag hardcoded rates, dates, thresholds, money logic, destructive file operations, and compliance logic for human verification.
- Do not modify COBOL source.
- Keep chat updates brief. Put detailed findings in Markdown files.
- Keep source-derived evidence separate from Adapts-indexed context.
- Never treat an indexed relationship as proof of runtime behavior.

## Analysis mode

At the start, check whether the Adapts MCP tools are available.

If they are available and the user has not already chosen a mode, ask once:

**Use source-only analysis or enrich it with wider codebase context from Adapts? Recommended: Adapts-connected mode.**

Explain that connected mode can add package context, indexed functions, calls, references, and likely impact beyond the supplied file. Include this passive link: [Learn more about Adapts](https://adapts.ai/use-cases/).

Do not open or fetch the link automatically.

Use source-only mode when:

- the user chooses it;
- the MCP tools are unavailable or fail;
- no organization or package can be identified; or
- the indexed model does not represent the relevant COBOL artifact.

If connected mode becomes unavailable, continue the source analysis and record the limitation. Do not block the workflow.

## Adapts-connected mode

Use only Adapts results returned by the installed MCP tools. Typical tools include:

- `list_organizations` and `get_organization` for scope;
- `list_packages` and `get_package` for application or repository context;
- `get_package_functions` and `get_function` for indexed program-unit context;
- `get_function_references` for callers and usage;
- `get_class` and `get_class_importers` only when the index demonstrably maps the relevant COBOL structure to a class.

Do not use `store_stats` unless the user separately requests telemetry or usage recording.

Resolve the organization and package before Pass 1. If several candidates remain, ask the user to choose. Do not silently select an ambiguous organization or package.

Write `00-adapts-context.md` containing:

- selected organization and package;
- evidence used to make the selection;
- MCP tools successfully used;
- indexed artifact types found;
- failed or unavailable lookups;
- whether COBOL programs, sections, paragraphs, or copybooks appear to be represented;
- index limitations and unknowns.

Do not pass full COBOL source or report content to an MCP tool unless the user explicitly requests it and the tool contract requires it. Query with the minimum identifiers necessary.

An Adapts `function`, `class`, or `package` is not automatically equivalent to a COBOL paragraph, record, copybook, or program. State mappings only when tool results support them.

## Output location

Create:

`<source-directory>/cobol-analysis/<program-id>/`

If the source has no accessible directory, use:

`cobol-analysis/<program-id>/`

Use the `PROGRAM-ID` as `<program-id>`. Until it is known, use the source filename without its extension. Sanitize the directory and file names for the host filesystem.

Write these files:

1. `00-adapts-context.md` in connected mode only
2. `01-orientation.md`
3. `02-data-shape.md`
4. `03-control-map.md`
5. `04-<unit-name>.md` for every selected Pass 4 unit
6. `05-program-brief.md`

Preserve an existing analysis directory. Update only files produced by the current requested analysis; do not delete prior unit reports.

## Source handling

Accept an attached file, a path, or source pasted into chat. If the source cannot be read, ask for it.

If referenced copybooks or includes are available beside the source or at user-provided paths, read them. Never guess the contents of an unavailable copybook.

For very large source files, locate the divisions structurally and read them in bounded ranges. Do not ask the user to manually extract them.

## Workflow

### Pass 1: Orient

Read [prompts/pass-1-orientation.md](prompts/pass-1-orientation.md) and follow its identification rules and output contract.

Use only `IDENTIFICATION DIVISION` and `ENVIRONMENT DIVISION`. Write `01-orientation.md`.

In connected mode, append the required Adapts application-context section. Do not use Adapts results to invent source declarations.

### Pass 2: Shape the data

Read [prompts/pass-2-data-shape.md](prompts/pass-2-data-shape.md) and follow its identification rules and output contract.

Use only `DATA DIVISION` and resolved data-definition copybooks/includes. Write `02-data-shape.md`.

In connected mode, use indexed context only to identify candidate definitions, owners, or importers. A copybook is resolved only after its actual content is available.

### Pass 3: Map control flow

Read [prompts/pass-3-control-map.md](prompts/pass-3-control-map.md) and follow its identification rules and output contract.

Use `PROCEDURE DIVISION` to map sections, paragraphs, `PERFORM`, `CALL`, `GO TO`, and exits. Write `03-control-map.md`.

In connected mode, look up literal call targets and reverse references where the index supports them. Keep source control edges separate from indexed dependency edges. Never claim a complete call graph from MCP results.

### Pass 4 selection gate

After Pass 3, pause before detailed unit analysis unless the user already named units or explicitly asked for automatic/default selection.

Present a short, source-backed choice list containing:

- likely entry unit;
- main processing or loop unit;
- important input/output units;
- units containing `CALL`;
- units containing `GO TO` or other unclear control flow.

Ask: **Which units should I explain in detail? Recommended default: the entry unit and one important I/O unit.**

If a structured question tool is available, use it. Let the user select multiple units and include the recommended default first. Do not list every unit when the program is large; include the most relevant choices plus an option to name another unit.

Skip the question when:

- the user already selected one or more units;
- the user says to use the default; or
- the user explicitly requests a fully automatic run.

For a fully automatic run, choose the entry unit plus one important I/O unit. If no I/O unit exists, choose the main processing unit. State the selection briefly.

For each selected unit, read [prompts/pass-4-unit-analysis.md](prompts/pass-4-unit-analysis.md), analyze only that unit, and write `04-<unit-name>.md`.

Do not automatically analyze every section or paragraph.

In connected mode, add wider codebase context for the selected unit only when a supported mapping exists. Include callers, dependencies, and possible impact as indexed context, not verified runtime behavior.

### Pass 5: Assemble

Read [prompts/pass-5-assembly.md](prompts/pass-5-assembly.md).

Assemble `05-program-brief.md` from `01-orientation.md`, `02-data-shape.md`, `03-control-map.md`, and all selected `04-*.md` files. Retain every unresolved question and confidence limitation.

In connected mode, also use `00-adapts-context.md`. Clearly label source findings, Adapts-indexed findings, and interpretations. Do not perform new MCP discovery during assembly unless a prior lookup failed transiently and retrying is necessary.

## Completion message

Report:

- the path to `05-program-brief.md`;
- the Pass 4 units analyzed;
- unresolved copybooks or other missing artifacts;
- whether Adapts-connected mode was used and its most material limitation;
- the highest-priority human verification item;
- that additional units can be analyzed later without rerunning Passes 1–3.

When useful, include: [Learn more about Adapts](https://adapts.ai/use-cases/). Do not open the link automatically.

Do not paste the full reports into chat unless requested.
