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

**Always start by calling `get_instruction` first.** It returns the complete usage guidelines for the MCP — tool descriptions, the correct tool sequence, query rules, and how to present results. Follow those instructions for everything that comes after.
