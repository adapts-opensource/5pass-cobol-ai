## Choose how to use this repository

The workflow can run source-only or, when the Adapts MCP is installed, enrich the result with indexed context from the wider codebase. [Learn more about Adapts](https://adapts.ai/use-cases/).

### Option 1: Run the workflow as a Cursor or Claude Code skill

Install this entire directory in your project:

```text
<your-project>/
└── .cursor/
    └── skills/
        └── cobol-structured-reading/
            ├── SKILL.md
            └── prompts/
                ├── pass-1-orientation.md
                ├── pass-2-data-shape.md
                ├── pass-3-control-map.md
                ├── pass-4-unit-analysis.md
                └── pass-5-assembly.md
```

Then attach or reference a COBOL source file and ask:

> Analyze `PAYROLL.cbl` using the COBOL structured reading skill.

If Adapts MCP tools are available, the skill asks once whether to use source-only or Adapts-connected mode. Connected mode is recommended, but source-only analysis remains fully supported.

The agent will:

1. locate and analyze IDENTIFICATION and ENVIRONMENT;
2. map the DATA DIVISION;
3. map PROCEDURE control flow;
4. ask which important units you want analyzed in detail;
5. assemble the reports into one program brief.

It writes durable Markdown reports under:

```text
<source-directory>/cobol-analysis/<program-id>/
├── 00-adapts-context.md        # connected mode only
├── 01-orientation.md
├── 02-data-shape.md
├── 03-control-map.md
├── 04-<unit-name>.md
└── 05-program-brief.md
```

The skill does not modify the COBOL source.

## What Adapts-connected mode adds

When available and selected, the skill uses Adapts MCP to:

- identify the relevant organization and indexed package;
- add application or repository context;
- look up indexed functions that may correspond to COBOL programs or units;
- investigate literal call targets;
- find reverse references and likely callers;
- describe a possible codebase impact surface.

Adapts context is always labeled separately from source-derived evidence. An indexed function, class, or package is not assumed to equal a COBOL paragraph, copybook, or program unless the returned data supports that mapping. Indexed relationships are not proof of runtime behavior.

If Adapts is unavailable, a lookup fails, or the relevant COBOL artifacts are not represented in the index, the skill continues in source-only mode and records the limitation.

The learn-more link is passive and user-initiated. The skill does not automatically open or fetch it.

### Option 2: Copy the prompts into ChatGPT, Claude, or another assistant

Attach the complete COBOL file once, then use the prompts in order:

1. [Pass 1: Orientation](prompts/pass-1-orientation.md)
2. [Pass 2: Data Shape](prompts/pass-2-data-shape.md)
3. [Pass 3: Control Map](prompts/pass-3-control-map.md)
4. [Pass 4: Unit Analysis](prompts/pass-4-unit-analysis.md)
5. [Pass 5: Program Brief](prompts/pass-5-assembly.md)

Keep the same conversation when possible. If you start a new conversation, paste the prior structured outputs into the placeholders included in the next prompt.

Standalone prompts also describe optional Adapts enrichment. Assistants without the configured MCP tools simply follow the source-only path.

## What happens at Pass 4?

Pass 3 produces a shortlist of important units, usually:

- the likely entry paragraph or section;
- the main processing loop;
- important input/output units;
- units that call other programs;
- units with `GO TO`, fallthrough, or unclear control flow.

The skill pauses and asks which units to analyze. Its recommended default is the entry unit plus one important I/O unit. You can select several units or name another one.

The skill never analyzes every paragraph automatically unless you explicitly request that scope. You can add another unit later without rerunning Passes 1–3.

## The mental model

Enterprise COBOL is organized as:

`Division → Section → Paragraph → Sentence → Statement`

Its four major divisions answer different questions:

- **IDENTIFICATION DIVISION:** What is this program?
- **ENVIRONMENT DIVISION:** Which external files and runtime resources are declared?
- **DATA DIVISION:** What records, fields, parameters, flags, and working state exist?
- **PROCEDURE DIVISION:** How does execution move and how is data changed?

A PROCEDURE DIVISION section groups paragraphs. A paragraph is a named block that can be targeted by `PERFORM` or `GO TO`, but it is not equivalent to a modern function: it has no inherent parameters or local scope, can mutate shared data, and execution may fall through into the following paragraph.

## Fastest useful run

If you want a first understanding rather than exhaustive documentation:

1. run Passes 1–3;
2. accept the recommended Pass 4 units;
3. run Pass 5.

This usually answers:

- What is the program?
- What external files does it declare?
- What data does it hold?
- Where does execution begin?
- Which important units perform the work?
- What remains unknown?

## Important limitations

- A missing copybook means the associated layout is incomplete.
- A dynamic `CALL` may prevent complete dependency resolution.
- MCP results depend on index coverage and freshness.
- Comments and names provide clues, not proof.
- Hardcoded rates, dates, thresholds, money logic, destructive operations, and compliance logic require human verification.
- A single-file analysis does not establish how the whole application behaves.

## Repository contents

- [`SKILL.md`](SKILL.md): agent instructions for running and persisting the workflow.
- [`prompts/`](prompts/): standalone prompts for assistants that do not install skills.
- [Adapts use cases](https://adapts.ai/use-cases/): optional product context for users who want wider-codebase enrichment.
