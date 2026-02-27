---
name: swarm-status
description: Show the current state of the agent swarm — teams, agents, prompts, and configs. Use when you need to understand what's built, what's missing, or get a quick inventory.
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
---

## Task Tracking
Before starting, create a task list using TodoWrite with the major steps below. Mark each completed as you finish it.

**Tip:** This skill reads many files and generates a large report. Consider using subagents if running alongside other tasks to preserve main context space.

Run a full inventory of the agent swarm and report:

## 1. Teams Configured
Read all YAML files in `config/teams/` and list each team with its leader and specialists.

## 2. Prompts Available
List all `.md` files in `src/prompts/library/` and check that every agent referenced in the YAML configs has a matching prompt file.

## 3. Code Health
- Check that all Python files in `src/` parse without syntax errors: `python -m py_compile <file>`
- List any TODO/FIXME/HACK comments in the codebase

## 4. Dependencies
- Check if venv exists and has all required packages installed
- Compare installed packages against `pyproject.toml` dependencies

## 5. Missing Pieces
Flag anything that looks incomplete:
- YAML configs referencing prompt files that don't exist
- Agent classes imported but not used
- Teams defined in YAML but not wired into `main.py`

Present as a clean summary table.

## Parallelism
For large projects, dispatch parallel subagents: one for teams/YAML inventory, one for prompt coverage, one for code health (py_compile + TODOs), one for dependency checks. Each subagent returns its section of the report. Merge results into the final summary table.

## Next Steps
If missing pieces found, use `/add-team` to scaffold them or `/techdebt` to audit code quality.
