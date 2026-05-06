---
name: cortex
description: >
  Cortex gives you access to Gorgias domain knowledge, metric definitions, business rules,
  table schemas, and live BigQuery data through the Context Layer MCP. Use it whenever the
  user needs to retrieve information about Gorgias — whether for writing a doc, answering
  a question, preparing a meeting, building a dashboard, debugging a pipeline, or doing
  data analysis. Proactively use this skill when the user's request involves Gorgias
  business metrics, domain concepts, data definitions, table schemas, customers, revenue,
  churn, product usage, sales, or any information that the Context Layer might hold.
---

# Cortex

Use the **context-layer MCP** to answer any Gorgias-related question.

**Always call `get_instruction` first.** It returns the full operating manual — tool sequence, query rules, catalog navigation, and guardrails. Follow those instructions for everything after.

---

## What Cortex is good for

| Use case | What to ask |
|---|---|
| Account counts by bucket | Query `dim_accounts` filtered by bucket criteria |
| ARR and revenue data | `dim_accounts.arr` or `dim_subscriptions` |
| AI Agent automation rates | `dim_ai_agents.ai_agent_automation_rate_28` |
| AAO automation rates | `dim_accounts.aao_automation_rate_28` |
| Configuration gaps | `dim_ai_agents` — knowledge sources, channels, guidances |
| Interaction volume | `dim_automate_interactions` or `dim_accounts.total_interactions_28` |
| Metric definitions | `get_node` on the relevant topic — definitions live in the knowledge graph |
| Column names before writing SQL | Always verify via `get_node` on the target table — never guess |
| Overlay flag counts | Query `dim_accounts` for `churn_risk_score`, `is_ai_agent_support_ever_enabled`, `is_ai_sales_agent_enabled` |

## What Cortex is NOT for

- Real-time customer support tickets (use Gorgias app)
- Email send logs (use Customer.io MCP)
- Campaign content or copy (use copywriter skill)
- Candu component state (use Candu directly)

---

## Tool sequence

1. `get_instruction` — always first, returns the operating manual
2. `get_node` — navigate to the relevant topic, table, or metric to get column names and SQL patterns
3. `execute_query` — run the SQL (returns a job ID)
4. `get_query_results` — fetch rows from the job ID
5. `provide_sql` — share the final validated SQL with Sara if she needs it

**Never skip step 2.** Column names in `dim_accounts` and related tables change. Always verify the exact name before writing SQL — do not guess from memory or prior sessions.

---

## Common tables

| Table | What's in it |
|---|---|
| `dim_accounts` | One row per account — status, plan, ARR, automation rates, flags, CSM/AM info |
| `dim_ai_agents` | AI Agent configuration per account — knowledge sources, channels, guidances, automation rate |
| `dim_automate_interactions` | Interaction volume over time |
| `accounts_history` | Historical snapshots of account state — use for trends |
| `dim_subscriptions` | Subscription and billing data |

---

## Guardrails

- Always verify column names via `get_node` before writing SQL
- Report live query results — never cite numbers from memory or prior sessions
- If Cortex is unavailable, flag it explicitly and use reference counts marked as approximate
