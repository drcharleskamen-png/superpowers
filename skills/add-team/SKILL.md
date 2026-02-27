---
name: add-team
description: Add a new team to the agent swarm with leader + specialists, YAML config, and prompt files
disable-model-invocation: true
argument-hint: "[team-name] [description]"
---

## Task Tracking
Before starting, create a task list using TodoWrite with the major steps below. Mark each completed as you finish it.

Add a new team called "$0" to the agent swarm.

## Steps

<HARD-GATE>
MUST read at least one existing team YAML config before creating a new one. This ensures you follow the exact schema.
</HARD-GATE>

1. **Create YAML config** at `config/teams/$0.yaml`
   - Follow the exact schema of existing team configs (read one first)
   - Include: team id, name, description, leader config, 3 specialist configs
   - Each agent needs: id, name, role, prompt_file, model, max_tokens, temperature, capabilities

2. **Create prompt files** in `src/prompts/library/`
   - One `.md` file for the team leader: `tl_$0.md`
   - One `.md` file per specialist: `specialist_<name>.md`
   - Follow the format of existing prompts (read 2-3 for reference)
   - Include: role definition, responsibilities, workflow, success metrics, communication

3. **Verify** the team loads correctly:
   ```bash
   source .venv/bin/activate
   python3 -c "
   import sys, os; sys.path.insert(0, '.')
   os.environ['ANTHROPIC_API_KEY'] = 'test-key'
   from src.llm.client import AnthropicClient
   from src.prompts.loader import PromptLoader
   from src.teams.registry import TeamRegistry
   client = AnthropicClient()
   loader = PromptLoader()
   registry = TeamRegistry(client, loader)
   teams = registry.load_all()
   for t in teams.values():
       print(t)
   "
   ```

4. **Wire into hierarchy** if it belongs to a new department:
   - If the default department: it auto-discovers via TeamRegistry, no changes needed
   - If new department: create a new DepartmentHeadAgent in `main.py:build_swarm()`

5. **Update CLAUDE.md** with the new team's purpose and any conventions.

## Important
- Team IDs must be lowercase with underscores (e.g., `competitive_intel`)
- Prompt file names must match the `prompt_file` field in the YAML exactly (without `.md`)
- The TeamRegistry auto-discovers YAML files — no registration code needed

## Next Steps
After completion, run `/swarm-status` to verify the new team loads correctly. Update CLAUDE.md with the new team.
