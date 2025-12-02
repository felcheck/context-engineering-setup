# CLAUDE.md

> Instructions and context for Claude Code working on this project.

---
## Project Config
<!-- ✏️ EDIT THIS SECTION FOR EACH PROJECT -->

```yaml
name: [Project Name]
stack: [e.g., TypeScript, Next.js 14, Tailwind, PostgreSQL, Prisma]
language: [e.g., TypeScript (strict), Python 3.11, Go 1.21]
package_manager: [pnpm | npm | yarn | bun | pip | poetry | cargo | go mod]
test_framework: [e.g., Vitest, Jest, Pytest, Go test]
```

### Commands
<!-- Replace with your actual commands -->

| Task | Command |
|------|---------|
| Install | `[package_manager] install` |
| Dev | `[package_manager] dev` |
| Build | `[package_manager] build` |
| Lint | `[package_manager] lint` |
| Typecheck | `[package_manager] typecheck` |
| Test (all) | `[package_manager] test` |
| Test (unit) | `[package_manager] test:unit` |
| Test (single) | `[package_manager] test -- -t "test name"` |

### File Structure
<!-- Adjust to match your project layout -->

```
src/
├── [app/pages]     # Routes/pages
├── [components]    # UI components
├── [lib/utils]     # Shared utilities
├── [services]      # Business logic
├── [types]         # Type definitions
tests/
├── unit/
├── integration/
├── e2e/
```

---
<!-- ⚠️ BELOW THIS LINE: Framework config — edit sparingly -->

## Coding Standards

### Naming Conventions
<!-- Adjust to your language/framework conventions -->
- **Functions/Variables:** camelCase | snake_case
- **Components/Classes:** PascalCase
- **Constants:** UPPER_SNAKE_CASE
- **Files:** kebab-case | snake_case | PascalCase (for components)

### Code Style
<!-- Add your project-specific style rules -->
- [Style rule 1]
- [Style rule 2]
- [Style rule 3]

## Boundaries

### ✅ Always
- Run lint and typecheck before committing
- Write or update tests for any code changes
- Follow naming conventions above

### ⚠️ Ask First
- Adding new dependencies
- Modifying database schema
- Major refactors affecting multiple files

### 🚫 Never
- Commit secrets or API keys
- Modify `node_modules/`, `vendor/`, or lock files manually
- Remove failing tests without fixing the issue
- Push directly to `main`

---

## Agent Workflow

Use a three-phase approach for any non-trivial task:

### Phase 1: Research
- **Goal:** Understand relevant files, data flow, and root causes
- **Output:** `thoughts/shared/research/<issue-name>.md`
- **Use:** `/project:research <description>`

### Phase 2: Plan
- **Goal:** Create phased implementation plan with specific files and tests
- **Output:** `thoughts/shared/plans/<issue-name>.md`
- **Use:** `/project:plan <description>` (after research exists)

### Phase 3: Implement
- **Goal:** Execute plan with TDD — failing tests first, then fix
- **Use:** `/project:implement <plan-file>`

---

## Context Engineering

### Why This Matters
The context window is finite (200k tokens). Every tool call, file read, and message accumulates. Noisy context = worse outputs.

### Core Principles

1. **Target 50% context utilization** — Leave room for implementation
2. **Intentional compaction** — Write progress to markdown, then `/clear` and restart
3. **Sub-agents for exploration** — Offload noisy search/read operations to sub-agents so main context stays clean

### Sub-Agent Usage

Sub-agents are for **context control**, not role-play:

```
✅ Good sub-agent prompt:
"Find all files related to [feature]. Return:
- File paths with line numbers
- Key function names
- 1-sentence summary per file
Use only: Read, Glob, Grep. No Bash."

❌ Bad sub-agent prompt:
"You are a senior engineer. Please investigate..."
```

### Session Workflow for Complex Tasks

```
1. START
   └── Read CLAUDE.md + relevant research/plan file

2. WORK
   └── Execute one phase at a time
   └── Run tests frequently

3. CHECKPOINT (after each phase)
   └── Update plan file with: what's done, what failed, next steps

4. COMPACT (when context > 50% or getting noisy)
   └── Write progress summary to file
   └── /clear
   └── Start fresh: "Read [file] and continue Phase N"

5. DONE
   └── All tests pass
   └── Plan marked complete
```

### When Stuck

1. Stop — don't keep trying the same approach
2. Write current understanding to a progress file
3. `/clear`
4. Re-read problem + progress with fresh context
5. Try different approach or ask for clarification
