---
name: analytics
description: Query swarm execution analytics — costs, token usage, agent performance, trends. Uses the SQLite analytics database.
allowed-tools:
  - Bash
  - Read
---

Query the agent swarm analytics database to answer questions about execution history, costs, token usage, and agent performance.

## Database Location

`data/swarm_analytics.db`

## Schema Reference

### `runs` — One row per swarm execution
| Column | Type | Description |
|--------|------|-------------|
| run_id | TEXT PK | UUID |
| task_name | TEXT | Task name |
| task_description | TEXT | The user's original task text |
| task_type | TEXT | Usually "general" |
| status | TEXT | "completed" or "failed" |
| total_tokens | INTEGER | Sum of all agents' own tokens |
| total_input_tokens | INTEGER | Total input tokens from API |
| total_output_tokens | INTEGER | Total output tokens from API |
| total_requests | INTEGER | Number of API calls |
| total_errors | INTEGER | Number of API errors |
| cost_usd | REAL | Total cost using model-specific rates |
| execution_time | REAL | Total wall-clock seconds |
| created_at | TEXT | ISO 8601 timestamp |
| agent_count | INTEGER | Number of agents that participated |

### `agent_results` — One row per agent per run (flattened hierarchy)
| Column | Type | Description |
|--------|------|-------------|
| run_id | TEXT FK | References runs |
| agent_id | TEXT | e.g. "content_creator" |
| agent_name | TEXT | Human-readable name |
| agent_tier | TEXT | "orchestrator", "department_head", "team_leader", "specialist" |
| parent_agent_id | TEXT | Parent in hierarchy (NULL for root) |
| status | TEXT | "completed" or "failed" |
| tokens_used | INTEGER | This agent's OWN tokens only |
| execution_time | REAL | Seconds |
| tree_depth | INTEGER | 0=orchestrator, 1=dept_head, 2=leader, 3=specialist |

### `api_requests` — One row per Claude API call
| Column | Type | Description |
|--------|------|-------------|
| run_id | TEXT FK | References runs |
| model | TEXT | e.g. "claude-sonnet-4-20250514" |
| input_tokens | INTEGER | Input tokens |
| output_tokens | INTEGER | Output tokens |
| cost_usd | REAL | Cost for this call (model-specific rates) |
| elapsed | REAL | API response time in seconds |
| timestamp | REAL | Unix timestamp |

## How to Query

Use `sqlite3` CLI:

```bash
sqlite3 -header -column data/swarm_analytics.db "YOUR SQL HERE"
```

For multi-line queries:
```bash
sqlite3 -header -column data/swarm_analytics.db <<'SQL'
SELECT ...
FROM ...
SQL
```

## Example Queries

**Total spend this week:**
```sql
SELECT COUNT(*) as runs, ROUND(SUM(cost_usd), 4) as total_cost,
       SUM(total_tokens) as tokens
FROM runs WHERE created_at >= date('now', '-7 days');
```

**Average cost per run:**
```sql
SELECT ROUND(AVG(cost_usd), 4) as avg_cost,
       ROUND(AVG(total_tokens)) as avg_tokens,
       ROUND(AVG(execution_time), 1) as avg_seconds, COUNT(*) as num_runs
FROM runs;
```

**Top token-consuming agents:**
```sql
SELECT agent_id, agent_tier, COUNT(*) as times_used,
       SUM(tokens_used) as total_tokens, ROUND(AVG(tokens_used)) as avg_tokens
FROM agent_results WHERE tokens_used > 0
GROUP BY agent_id ORDER BY total_tokens DESC;
```

**Cost breakdown by model:**
```sql
SELECT model, COUNT(*) as calls,
       SUM(input_tokens) as input_tok, SUM(output_tokens) as output_tok,
       ROUND(SUM(cost_usd), 4) as total_cost
FROM api_requests GROUP BY model ORDER BY total_cost DESC;
```

**Most expensive runs:**
```sql
SELECT SUBSTR(run_id, 1, 8) as run, SUBSTR(task_description, 1, 50) as task,
       ROUND(cost_usd, 4) as cost, total_tokens, agent_count, execution_time
FROM runs ORDER BY cost_usd DESC LIMIT 10;
```

**Daily cost trend:**
```sql
SELECT DATE(created_at) as day, COUNT(*) as runs,
       ROUND(SUM(cost_usd), 4) as cost, SUM(total_tokens) as tokens
FROM runs GROUP BY DATE(created_at) ORDER BY day DESC;
```

**Agent failure rates:**
```sql
SELECT agent_id, agent_tier, COUNT(*) as total,
       SUM(CASE WHEN status='failed' THEN 1 ELSE 0 END) as failures,
       ROUND(100.0 * SUM(CASE WHEN status='failed' THEN 1 ELSE 0 END) / COUNT(*), 1) as fail_pct
FROM agent_results GROUP BY agent_id HAVING total > 1 ORDER BY fail_pct DESC;
```

**API latency by model:**
```sql
SELECT model, COUNT(*) as calls,
       ROUND(AVG(elapsed), 2) as avg_s, ROUND(MIN(elapsed), 2) as min_s,
       ROUND(MAX(elapsed), 2) as max_s
FROM api_requests GROUP BY model;
```

**Tokens by agent tier:**
```sql
SELECT agent_tier, COUNT(*) as agents, SUM(tokens_used) as total_tokens,
       ROUND(AVG(tokens_used)) as avg_tokens
FROM agent_results WHERE tokens_used > 0
GROUP BY agent_tier ORDER BY total_tokens DESC;
```

## Instructions

When the user asks an analytics question:
1. Determine which table(s) to query
2. Write SQL appropriate for SQLite (use `date()`, `strftime()` for date ops)
3. Execute via `sqlite3` CLI as shown above
4. Present results clearly and add brief interpretation

If the database doesn't exist, inform the user to run a swarm task first:
`python main.py "your task"`
