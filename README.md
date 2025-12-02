# Claude Code Context Engineering Framework

A structured workflow for using Claude Code on complex codebases, based on the "Advanced Context Engineering for Coding Agents" approach.

## What This Is

This framework helps you get better results from Claude Code by:

1. **Structuring work into phases** — Research → Plan → Implement
2. **Managing context intentionally** — Keep the context window clean and focused
3. **Creating durable artifacts** — Markdown docs that humans review, not just chat history
4. **Using TDD with agents** — Write failing tests first, then implement

## Quick Start

### 1. Clone or Copy This Repo

```bash
# Clone into your project (or copy the files)
git clone https://github.com/YOUR_USERNAME/context-engineering-setup.git
cd context-engineering-setup
```

### 2. Configure CLAUDE.md

Open `CLAUDE.md` and edit **only the Project Config section** at the top:

```yaml
name: your-project-name
stack: TypeScript, Next.js 14, Tailwind, PostgreSQL
language: TypeScript (strict)
package_manager: pnpm
test_framework: Vitest
```

Update the **Commands table**:

| Task | Command |
|------|---------|
| Install | `pnpm install` |
| Dev | `pnpm dev` |
| Build | `pnpm build` |
| Lint | `pnpm lint` |
| Typecheck | `pnpm typecheck` |
| Test (all) | `pnpm test` |
| Test (unit) | `pnpm test:unit` |
| Test (single) | `pnpm test -- -t "test name"` |

Update the **File Structure** diagram to match your project layout.

### 3. (Optional) Customize Standards

Edit these sections if your project has specific conventions:
- Naming Conventions
- Code Style rules
- Boundaries (Always/Ask First/Never)

### 4. Start Using

You now have three slash commands available in Claude Code:

```
/project:research <issue description>
/project:plan <issue description>
/project:implement <plan file or description>
```

---

## Directory Structure

```
your-project/
├── CLAUDE.md                      # Main config (edit per project)
├── .claude/
│   └── commands/
│       ├── research.md            # /project:research command
│       ├── plan.md                # /project:plan command
│       └── implement.md           # /project:implement command
├── thoughts/
│   └── shared/
│       ├── research/              # Research doc outputs
│       └── plans/                 # Implementation plan outputs
└── ... (your project files)
```

---

## Workflow

### For Simple Tasks (< 30 min)

Skip research, go straight to plan → implement:

```
You: /project:plan Add validation for email field in signup form
     ... review the plan ...
You: /project:implement thoughts/shared/plans/email-validation.md
```

### For Complex Tasks (> 30 min or unfamiliar code)

Full workflow:

```
1. RESEARCH
   You: /project:research Why does the payment flow fail silently on timeout?
   ... Claude creates thoughts/shared/research/payment-timeout.md ...
   ... YOU REVIEW the research doc ...

2. PLAN
   You: /project:plan Fix payment timeout issue (see research)
   ... Claude creates thoughts/shared/plans/payment-timeout.md ...
   ... YOU REVIEW the plan ...

3. IMPLEMENT
   You: /project:implement thoughts/shared/plans/payment-timeout.md
   ... Claude executes phase by phase with TDD ...
```

### When to Use Each Phase

| Situation | Start With |
|-----------|------------|
| Bug in code you wrote yesterday | `/project:plan` |
| Bug in unfamiliar part of codebase | `/project:research` |
| New feature, clear requirements | `/project:plan` |
| New feature, unclear scope | `/project:research` |
| Refactor with known files | `/project:plan` |
| "Why does X happen?" | `/project:research` |

---

## Key Concepts

### Context Window Management

Claude Code has a 200k token context window. Everything accumulates:
- System prompts and tools (~20-30k)
- Your messages
- Claude's responses
- Every file read, search result, and tool call

**Target ~50% utilization.** When context gets heavy:

1. Ask Claude to write progress to a markdown file
2. Use `/clear` to reset
3. Start fresh: "Read [progress file] and continue with Phase N"

### Sub-Agents

Sub-agents (Task tool) run in **separate context windows**. Use them for:
- Parallel searches across the codebase
- Exploratory reads that might return lots of noise
- Any operation where you want results but not the journey

**Good sub-agent prompt:**
```
Find all files related to user authentication. Return:
- File paths with line numbers
- Key function names
- 1-sentence summary per file
Use only: Read, Glob, Grep. No Bash.
```

### TDD with Agents

Always write tests first:

```
Phase 1: Write failing tests    → Run tests → They fail ✓
Phase 2: Implement feature      → Run tests → They pass ✓
Phase 3: Cleanup/edge cases     → Run tests → Still pass ✓
```

This gives Claude clear success criteria and catches regressions immediately.

### Review Hierarchy

Where to focus your human attention:

| Artifact | Review Depth | Why |
|----------|--------------|-----|
| Research doc | **Deep** | Wrong understanding → 1000s of bad lines |
| Plan | **Deep** | Wrong plan → 100s of bad lines |
| Tests | **Careful** | Tests define correctness |
| Implementation | Skim | If tests pass and plan was good, code is likely fine |

---

## Example: Fixing a Bug

### 1. Start with Research

```
You: /project:research Users report "undefined" showing in profile name field
```

Claude creates `thoughts/shared/research/profile-undefined-bug.md`:

```markdown
# Research: Profile Name Undefined Bug

## Problem Statement
Users see "undefined" in the profile name field after updating their profile.

## Relevant Files
| File | Lines | Purpose |
|------|-------|---------|
| src/services/user.ts | 45-80 | Fetches and transforms user data |
| src/components/Profile.tsx | 120-145 | Renders profile info |

## Key Findings
- `getUserProfile()` returns `{ name: user.name }` but doesn't handle null
- Component renders `{user.name}` without fallback

## Hypotheses
1. Database has null names from legacy migration
2. Transform doesn't provide default value
```

**You review:** "Yep, that's the issue. Proceed."

### 2. Create Plan

```
You: /project:plan Fix profile undefined bug per research
```

Claude creates `thoughts/shared/plans/profile-undefined-bug.md`:

```markdown
# Plan: Fix Profile Undefined Bug

## Phase 1: Add Failing Test
- Create test that fetches user with null name
- Assert it returns empty string, not undefined

## Phase 2: Fix Transform
- Modify getUserProfile() to default null names to ""

## Phase 3: Add UI Fallback
- Update Profile.tsx to show "Anonymous" if name empty
```

**You review:** "Looks good, but let's show 'Set your name' instead of 'Anonymous'"

### 3. Implement

```
You: /project:implement thoughts/shared/plans/profile-undefined-bug.md
     Note: Use "Set your name" as fallback text, not "Anonymous"
```

Claude executes phase by phase, running tests after each.

---

## Tips

### Steering Research

If research comes back wrong ("there's no bug" when you know there is):
1. Add a concrete failing test case to your prompt
2. Be more specific about where to look
3. Tell it what you've already ruled out

### Keeping Plans Focused

Add "Out of Scope" section to prevent scope creep:

```markdown
## Out of Scope
- Refactoring the entire user service
- Adding new profile fields
- Performance optimization (separate issue)
```

### When Context Gets Messy

Signs you need to compact:
- Claude starts repeating itself
- Responses get slower
- It "forgets" things from earlier in the conversation
- You're past 50% context usage

### Parallel Execution

For independent tasks, ask Claude to use sub-agents in parallel:

```
You: Write unit tests for all 5 validation functions.
     Use parallel sub-agents, one per function.
```

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Commands not showing up | Ensure files are in `.claude/commands/` with `.md` extension |
| Research is wrong | Add specific test cases, rephrase, or manually point to files |
| Plan is too vague | Ask for specific file paths and line numbers |
| Implementation diverges from plan | Stop, update plan first, then continue |
| Context too noisy | Write progress to file, `/clear`, restart |

---

## Files Reference

| File | Purpose | Edit Frequency |
|------|---------|----------------|
| `CLAUDE.md` | Project config + framework | Once per project |
| `.claude/commands/research.md` | Research command | Rarely |
| `.claude/commands/plan.md` | Planning command | Rarely |
| `.claude/commands/implement.md` | Implementation command | Rarely |
| `thoughts/shared/research/*.md` | Research outputs | Per issue |
| `thoughts/shared/plans/*.md` | Plan outputs | Per issue |

---

## Attribution & Sources

This framework is based on concepts and techniques from the following sources:

### Primary Sources

**AI That Works — Episode #17: Advanced Context Engineering for Coding Agents**
- Video: [youtube.com/watch?v=42AzKZRNhsk](https://www.youtube.com/watch?v=42AzKZRNhsk)
- Repository: [github.com/ai-that-works/ai-that-works](https://github.com/ai-that-works/ai-that-works)
- Specific episode code: [2025-08-05-advanced-context-engineering-for-coding-agents](https://github.com/hellovai/ai-that-works/tree/main/2025-08-05-advanced-context-engineering-for-coding-agents)
- Creators: Vaibhav Gupta ([@hellovai](https://github.com/hellovai)) and Dexter Horthy
- Organization: [BoundaryML](https://boundaryml.com) (creators of BAML)

**HumanLayer — Advanced Context Engineering for Coding Agents (ACE-FCA)**
- Article: [Getting AI to Work in Complex Codebases](https://github.com/humanlayer/advanced-context-engineering-for-coding-agents/blob/main/ace-fca.md)
- Repository: [github.com/humanlayer/advanced-context-engineering-for-coding-agents](https://github.com/humanlayer/advanced-context-engineering-for-coding-agents)

### Additional References

**AGENTS.md Specification**
- Spec: [github.com/openai/agents.md](https://github.com/openai/agents.md)
- An emerging open standard for guiding AI coding agents, supported by GitHub Copilot, Cursor, OpenAI Codex, and others

**GitHub Blog — How to Write a Great agents.md**
- Article: [How to write a great agents.md: Lessons from over 2,500 repositories](https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/)
- Analysis of effective patterns across thousands of real-world agent config files

### Key Concepts Credited

| Concept | Source |
|---------|--------|
| Research → Plan → Implement workflow | AI That Works / BAML team |
| "Intentional compaction" | HumanLayer ACE-FCA |
| Sub-agents for context control | AI That Works Ep. 17 |
| Review hierarchy (research > plan > code) | AI That Works Ep. 17 |
| TDD-first with agents | AI That Works / BAML team |
| `thoughts/shared/` directory structure | BAML team workflow |

---

## License

This framework documentation is provided as-is for educational and practical use. Please refer to the original sources above for their respective licenses and terms.
