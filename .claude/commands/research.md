---
description: Research the codebase to understand an issue or feature area
---

# Research Task

You are researching the codebase to understand: $ARGUMENTS

## Before Starting

Read `CLAUDE.md` to understand:
- Project structure and file layout
- Key commands (test, lint, etc.)
- Naming conventions

## Instructions

1. **Launch parallel sub-agents** to explore different aspects:
   - Sub-agent 1: Find files directly related to the feature/bug
   - Sub-agent 2: Find related tests and test utilities
   - Sub-agent 3: Trace data flow (entry points → processing → output)

2. **Sub-agent prompts should be specific:**
   ```
   Search for [specific thing].
   Return a structured list:
   - File path with relevant line numbers
   - Key functions/classes involved
   - 1-sentence description of responsibility
   Use only: Read, Glob, Grep. Do not use Bash.
   ```

3. **Synthesize findings** into a research document

## Output Format

Create file: `thoughts/shared/research/<issue-slug>.md`

```markdown
# Research: [Issue Title]

## Problem Statement
[Clear description of the bug/feature]

## Reproduction
[Steps or test case that demonstrates the issue]

## Relevant Files

| File | Lines | Purpose |
|------|-------|---------|
| `path/to/file` | 45-120 | Handles X validation |
| `path/to/other` | 200-250 | Processes Y data |

## Data Flow
1. Entry point: `functionA()` in `file`
2. Calls: `functionB()` in `other`
3. Returns: result via `functionC()`

## Key Findings
- [Finding 1 with file:line reference]
- [Finding 2 with file:line reference]

## Hypotheses
1. [Likely root cause and why]
2. [Alternative explanation]

## Open Questions
- [Anything unclear that needs human input]
```

## Constraints

- Use only read-only tools in sub-agents (Read, Glob, Grep)
- Keep each sub-agent focused on one specific question
- If research reveals the issue is already fixed or non-existent, say so clearly
- Do not start implementing — research only
