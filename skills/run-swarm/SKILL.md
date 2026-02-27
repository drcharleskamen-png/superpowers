---
name: run-swarm
description: Run a task through the agent swarm and display results
disable-model-invocation: true
argument-hint: "[task description]"
---

## Task Tracking
Before starting, create a task list using TodoWrite with the major steps below. Mark each completed as you finish it.

Run the following task through the agent swarm:

**Task:** $ARGUMENTS

## Steps

<HARD-GATE>
MUST verify .env exists and has a valid ANTHROPIC_API_KEY before executing. Check with: `test -f .env && grep -q ANTHROPIC_API_KEY .env`
</HARD-GATE>

1. Ensure the venv is active and `.env` has a valid API key:
   ```bash
   source .venv/bin/activate
   ```

2. Run with execution trace:
   ```bash
   python main.py --trace "$ARGUMENTS"
   ```

3. If it fails:
   - Check for API key issues (401 errors)
   - Check for rate limiting (429 errors)
   - Review the error output and suggest fixes
   - If a code fix is needed, apply it and retry

4. After successful run, report:
   - Total tokens used
   - Estimated cost
   - Which teams and specialists were activated
   - Summary of the output quality

## Next Steps
After success, run `/analytics` to review costs and token usage. If the run failed, use the `systematic-debugging` skill to investigate root cause before retrying.
