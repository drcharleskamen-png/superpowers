---
name: test-agent
description: Test a single agent in isolation by sending it a task directly, bypassing the hierarchy
disable-model-invocation: true
argument-hint: "[agent-id] [task description]"
---

Test the agent "$0" in isolation with the task: "$1"

## Steps

1. Read the agent's prompt file to understand its role

2. Run a direct test:
   ```bash
   source .venv/bin/activate
   python3 -c "
   import asyncio, sys, os
   sys.path.insert(0, '.')
   from dotenv import load_dotenv
   load_dotenv()
   from src.core.models import Task, AgentConfig
   from src.llm.client import AnthropicClient
   from src.agents.specialist import SpecialistAgent
   from src.prompts.loader import PromptLoader

   async def test():
       client = AnthropicClient()
       loader = PromptLoader()
       config = AgentConfig(
           agent_id='$0',
           name='Test: $0',
           role='Test agent',
           prompt_file='$0',
       )
       agent = SpecialistAgent(config=config, llm_client=client)
       agent.load_prompt(loader.load('$0'))
       task = Task(name='Test', description='$1')
       result = await agent.process(task)
       print('Status:', result.status.value)
       print('Tokens:', result.tokens_used)
       print('Time:', result.execution_time, 's')
       print('---')
       print(result.content)
       print('---')
       print('Cost:', client.get_usage_summary())

   asyncio.run(test())
   "
   ```

3. Evaluate the output:
   - Does it match the agent's stated role?
   - Is the format correct?
   - Is the quality production-ready?
   - Any hallucinations or off-topic content?

4. Report findings and suggest prompt improvements if needed.
