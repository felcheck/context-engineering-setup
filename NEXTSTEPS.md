# Next Steps & Ideas

## Why `thoughts/shared/`?

The `shared/` folder pattern comes from the **BAML team's workflow** (Vaibhav Gupta and BoundaryML). It's designed for multi-agent and multi-session workflows.

### The Reasoning

1. **Shared = Visible to all agents/sessions** — When you have multiple Claude instances (parallel worktrees, main agent + sub-agents), the `shared/` folder contains artifacts that **any agent can read and write to**. It's the common scratchpad.

2. **Implicit contrast with "local/private"** — The naming suggests there could be:
   - `thoughts/shared/` — Durable artifacts (plans, research) that survive `/clear` and are meant for human review
   - `thoughts/local/` — Ephemeral notes, WIP drafts, or agent-specific context (not yet implemented)

3. **Filesystem as communication channel** — From Anthropic's multi-agent research: "Subagent output to a filesystem can minimize the 'game of telephone.' You can have Claude instances communicate with each other by giving them separate working scratchpads."

The `shared/` naming makes explicit that these files are the **coordination point** between human and agents, and between multiple agents.

---

## Potential Enhancements

### Add `thoughts/local/` for Ephemeral Context

```
thoughts/
├── shared/          # Persists, reviewed by humans, shared across sessions
│   ├── plans/
│   └── research/
└── local/           # Gitignored, session-specific, disposable
    └── scratchpad.md
```

Use case: Agent writes WIP notes to `local/` during exploration, then promotes findings to `shared/research/` when ready.

### Multi-Worktree Setup

For parallel development on complex features:

```bash
# Create separate worktrees for parallel work
git worktree add ../project-feature-a feature-a
git worktree add ../project-feature-b feature-b

# Run Claude in each with isolated context
cd ../project-feature-a && claude
cd ../project-feature-b && claude
```

Each worktree gets its own `thoughts/shared/` that can be merged back.

### Agent-Specific Scratchpads

For coordinating multiple sub-agents:

```
thoughts/
├── shared/
│   ├── plans/
│   └── research/
└── agents/
    ├── researcher/     # Sub-agent 1 writes here
    ├── implementer/    # Sub-agent 2 writes here
    └── coordinator.md  # Main agent reads all, coordinates
```

---

## Sources

- [Claude Code: Best practices for agentic coding](https://www.anthropic.com/engineering/claude-code-best-practices)
- [How we built our multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system)
- [Efficient Claude Code: Context Parallelism & Sub-Agents](https://www.agalanov.com/notes/efficient-claude-code-context-parallelism-sub-agents/)
- [AI That Works - Advanced Context Engineering (BAML team)](https://github.com/hellovai/ai-that-works/tree/main/2025-08-05-advanced-context-engineering-for-coding-agents)
- [Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
