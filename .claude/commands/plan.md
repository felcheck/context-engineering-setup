---
description: Create a phased implementation plan for a feature or bug fix
---

# Planning Task

You are creating an implementation plan for: $ARGUMENTS

## Before Starting

1. Read `CLAUDE.md` to understand project commands and structure
2. Check if research exists in `thoughts/shared/research/` — read it first if so
3. If no research exists and the task is complex, suggest running `/project:research` first

## Instructions

1. **Review context:**
   - Read any existing research document
   - Identify the specific files that need changes
   - Understand the test infrastructure

2. **Design phases:**
   - Each phase should be independently testable
   - Phase 1 is usually: write failing tests
   - Subsequent phases: implement changes to make tests pass
   - Final phase: cleanup, documentation, edge cases

3. **Be specific:**
   - Exact file paths and line numbers
   - Exact function/class names to modify or create
   - Exact test names and what they verify

4. **Use project commands:**
   - Reference test/lint/build commands from `CLAUDE.md`
   - Don't hardcode package manager — use whatever the project uses

## Output Format

Create file: `thoughts/shared/plans/<issue-slug>.md`

```markdown
# Plan: [Issue Title]

## Summary
[1-2 sentence description of what we're building/fixing]

## Research Reference
[Link to research doc if exists, or "N/A - simple task"]

## Success Criteria
- [ ] [Specific testable outcome 1]
- [ ] [Specific testable outcome 2]
- [ ] All existing tests still pass

---

## Phase 1: Add Failing Tests

**Files:**
- Create: `tests/path/feature-name.test.[ext]`

**Tests to write:**
1. `test/it [expected behavior]` — verifies [X]
2. `test/it [edge case behavior]` — verifies [Y]

**Run:** `[test command from CLAUDE.md]`

**Expected:** Tests fail (feature not implemented yet)

---

## Phase 2: Implement Core Logic

**Files:**
- Modify: `path/to/file` (lines X-Y)
- Create: `path/to/new-file`

**Changes:**
1. In `file`:
   - Add validation in `functionA()` at line X
   - Handle edge case in `functionB()` at line Y

**Run:** `[test command from CLAUDE.md]`

**Expected:** Tests pass

---

## Phase 3: Integration & Cleanup

**Files:**
- Modify: `path/to/integration-point`

**Changes:**
1. Wire up new logic
2. Add error handling

**Run:** `[full test command from CLAUDE.md]`

**Expected:** All tests pass

---

## Out of Scope

- [Thing we're explicitly NOT doing]
- [Related issue to address separately]

## Risks & Notes

- [Potential breaking change to watch for]
- [Dependency on external system]
```

## Constraints

- Do not implement anything — planning only
- Each phase must have a clear "run" command and "expected" outcome
- Include "Out of Scope" section to prevent scope creep
- If the plan requires decisions (e.g., "should we use approach A or B?"), ask before finalizing
