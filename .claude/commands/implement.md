---
description: Execute an implementation plan phase by phase
---

# Implementation Task

You are implementing: $ARGUMENTS

## Before Starting

Read `CLAUDE.md` to understand:
- Test commands to run after each phase
- Lint/typecheck commands to validate changes
- Project structure and conventions

## Instructions

1. **Locate the plan:**
   - If argument is a file path, read that plan
   - If argument is a description, find matching plan in `thoughts/shared/plans/`

2. **Execute phase by phase:**
   - Start with Phase 1 (usually: write failing tests)
   - Run the test command after each phase
   - Only proceed to next phase when current phase criteria met

3. **TDD workflow:**
   ```
   Write test → Run test (should fail) → Implement → Run test (should pass) → Next
   ```

4. **Update plan as you go:**
   - Mark completed phases with ✅
   - Note any deviations or issues encountered
   - If blocked, stop and document why

## Checkpointing

After completing each phase:

1. Run the specified test command from the plan
2. Update the plan file:
   ```markdown
   ## Phase 1: Add Failing Tests ✅

   **Status:** Complete
   **Notes:** [Any observations or changes from original plan]
   ```

3. If context is getting heavy (>50%), write a progress summary and suggest `/clear`

## Progress Summary Format

When compacting, create/update: `thoughts/shared/plans/<issue-slug>-progress.md`

```markdown
# Progress: [Issue Title]

## Current State
- Phase 1: ✅ Complete
- Phase 2: 🔄 In Progress (step 2 of 3)
- Phase 3: ⏳ Not Started

## What's Done
- Created test file with 3 test cases
- Implemented validation logic

## Current Blocker
[If any — otherwise "None"]

## Next Step
Continue Phase 2, step 3: Add error handling

## Files Modified
- `tests/path/feature.test.[ext]` (created)
- `src/path/feature.[ext]` (lines X-Y modified)
```

## Constraints

- Follow the plan — don't add unplanned features
- Run tests after every phase
- If tests fail unexpectedly, stop and diagnose before continuing
- If you need to deviate significantly from the plan, update the plan first
- Never skip the test-writing phase
