## Add Claude Code native task management support + extended skills library

### Summary

- Integrate Claude Code's native task management system (v2.1.16+, January 23 2026) into the Superpowers workflow, replacing ephemeral in-context todos with persistent, dependency-aware task tracking
- Add 9 new skills covering agent swarm operations, code quality auditing, test execution, analytics, and session management
- Update README to document CC task management support and new skills

### New Skills

| Skill | Description |
|-------|-------------|
| `swarm-status` | Full inventory of agent teams, prompts, configs, and missing pieces |
| `add-team` | Scaffold a new team with YAML config and prompt files |
| `run-swarm` | Execute a task through a hierarchical agent swarm with execution trace |
| `test-agent` | Test a single agent in isolation, bypassing the hierarchy |
| `techdebt` | Scan codebase for technical debt, duplication, dead code, inconsistencies |
| `analytics` | Query execution analytics (costs, tokens, agent performance) from SQLite |
| `session-notes` | End-of-session documentation with memory file updates |
| `quick-test` | Run tests with auto-framework detection and failure diagnosis |
| `review-pr` | Staff-engineer-level PR review with P0/P1/P2 severity findings |

### Claude Code Task Management Integration

Claude Code v2.1.16+ introduced a native task management system that replaces the original ephemeral TodoWrite/TodoRead tools. Key capabilities leveraged by this fork:

- Persistent task storage at `~/.claude/tasks/` (survives session restarts)
- Dependency tracking via `blockedBy` / `addBlocks` parameters
- Cross-session task visibility and progress broadcasting
- Tasks cannot transition to `in_progress` until all blocking tasks complete

Skills like `writing-plans` and `brainstorming` now create native tasks with dependency graphs, keeping the agent on rails during execution.

### Testing

1. Install the plugin: `/plugin install --source url https://github.com/drcharleskamen-png/superpowers.git`
2. Verify skills appear: `/help` should list all upstream + new skills
3. Start a session and trigger a planning workflow -- confirm native tasks are created with dependencies
4. Run `/review-pr`, `/quick-test`, or `/techdebt` to verify the new skills execute correctly
