---
name: linear-update
description: >
  Posts a status update to Sara's GRW Lifecycle Linear initiatives. Use when Sara
  wants to post a progress update, share what shipped, flag a risk, or summarize
  the current state of lifecycle work. Also triggers on "post a Linear update",
  "update Linear", "status update", "post to Linear", "Linear progress", "OKR update".
---

# Linear Status Update Agent

Post a structured status update to one or both of Sara's GRW Lifecycle initiatives in Linear.
Always fetch live data before writing — never summarize from memory alone.

---

## Sara's initiatives

Both initiatives have target date **2026-06-30** and report into parent KR:
`[KR Growth] — Increase SMB activation, adoption, and monetization to deliver $887k nnARR efficiently.`

OKR metrics source: Metabase dashboard 437 — https://prod.metabase.gorgias-decision-engine.com/dashboard/437

Always produce **two separate updates** — one per initiative. They must never share the same content.

---

### Initiative 1 — AI Agent Activation

**Linear ID:** `f5d9a0d6-b399-4464-97c2-31f96ae0abc9`
**OKR:** Increase share of self-serve AI Agent activated customers from 8% → 18%
**Metric:** % of self-serve AI Agent activated customers (vs. 18% target)
**Projects:** Optimisation Campaign Revamp — CSM & AM Routing, AI Underperformers, Lifecycle Operational Foundations, Revamp CX Weekly Email, Redefine product Growth activation definitions

**Routing rule:** Any work targeting **Automate or AI Agent accounts** belongs here — improving automation rates, onboarding AI Agent users, reducing underperformers, optimizing existing AI Agent adoption. These are accounts already on Automate or AI Agent.

---

### Initiative 2 — AI Agent Pipeline

**Linear ID:** `5648b092-9e95-46e2-aa58-ac70249803be`
**OKR:** Drive $399k AI Agent pipeline by converting product usage and intent into product-qualified leads
**Metric:** ARR pipeline generated (vs. $399k target)
**Projects:** Reactivation Campaign — Churned Trial Accounts, Cross-Sell Campaigns - Book a demo, Lifecycle Operational Foundations, Launch AI dormant Campaign

**Routing rule:** Any work targeting **Helpdesk-only accounts converting to AI Agent** belongs here — cross-sell campaigns, demo booking nudges, reactivation of churned accounts, converting Helpdesk merchants into AI Agent buyers. These are accounts that don't yet have AI Agent.

---

**When unsure which initiative a piece of work belongs to:** ask Sara before assigning it. Do not guess — misrouting dilutes both OKR stories.

---

---

## Step 0 — Fetch live data (run all in parallel)

Before writing anything, pull current state from all sources simultaneously:

### 0a — Linear: initiative + project status
Use `get_initiative` on both initiative IDs with `includeProjects: true`:
- Current health and status of each initiative
- All linked projects and their statuses
- Any recent status updates (to avoid repeating content)
- Open issues / blockers tagged to the initiative

### 0b — Notion: campaign registry
Use `notion-fetch` on the campaign registry (https://www.notion.so/2fd1ae2178f58027b159c1fffbe34332):
- Which campaigns are live, in QA, or in draft
- Campaign names and their Notion page links (for linking in the update)

### 0c — Customer.io: active campaign status
Use the CIO MCP (`cio_api`) to check active journeys:
- Which campaigns are currently active vs. draft vs. paused
- Recent journey entry counts as a proxy for reach

### 0d — Granola: recent meeting discussions
Use `query_granola_meetings` to search meetings from the past 7 days mentioning "lifecycle", "GRW", "AI Agent", or "campaign":
- Decisions made or commitments given in syncs
- Blockers raised and who owns them
- Stakeholder names mentioned — use these in the update

### 0e — Slack: recent messages
Use `slack_search_public_and_private` to search past 7 days for "lifecycle", "GRW lifecycle", or specific campaign names:
- Progress signals not formally documented elsewhere
- Cross-functional feedback on shipped work
- Escalations or urgent items → feed these into Discussion Items

---

## Step 1 — Fetch OKR metric from Metabase

Pull the current OKR headline metric to populate the Current Progress line:
- Initiative 1: % of self-serve AI Agent activated customers (vs. 18% target)
- Initiative 2: ARR pipeline generated (vs. $399k target)

Source: dashboard 437. If unavailable, leave `[fetch from dashboard 437]` as a placeholder.

---

## Step 2 — Draft the update

Default: produce both updates. Ask Sara only if she explicitly wants just one.

Present them clearly separated in chat — **Update 1: AI Agent Activation** then **Update 2: AI Agent Pipeline** — each a complete, self-contained status update with its own metric, accomplishments, blockers, and next steps drawn only from its initiative's projects.

Use this exact structure for each — mirror the formatting and emoji patterns from the reference examples:

```
Current Progress: [X]% | Target: [Y]% | Gap: [Z]pp

⚡ Top Line: [1–2 sentence executive summary of the single most important thing this week — campaign launched, blocker hit, experiment kicked off]

✅ Accomplished This Week

[emoji] [Project or campaign name] — [Linear issue ID linked to https://linear.app/gorgias/issue/[ID]]
[Detail of what was done, who was involved, current status. Be specific: names, numbers, dates.]

[emoji] [Next project/campaign name] — [Linear issue ID]
[Detail]

⛔️ Blockers

🔗 [Blocker title] — [Linear issue ID]
[What is blocked, what is needed, who owns resolution, ETA if known]

🔗 [Second blocker if any]
[Details]

➡️ Next Steps & Priorities

[Linear issue ID linked] [Task name] — [1-sentence description]. [Priority: P0/P1] — due [date]
[Linear issue ID linked] [Task name] — [Priority] — due [date]

💬 Discussion Items

[Topic from Granola/Slack] — [Linear issue ID if applicable]
[1–2 sentences: what was discussed, what decision or open question remains, who is involved]
```

**Emoji guide for Accomplished items** — pick the most fitting:
- 🎯 targeting / segmentation / campaign architecture
- 📧 email design or content
- 🚀 experiment launched or new initiative kicked off
- 🔧 technical setup, integration, or infra work
- 📊 data / analytics / reporting
- ✅ general completion (use sparingly — prefer specific emojis)

**Rules:**
- Every Linear issue ID must be a live link: `[GRWLIF-104](https://linear.app/gorgias/issue/GRWLIF-104)`
- Every campaign name must link to its Notion page when available
- Any metric cited (account count, ARR, rate) must reference Metabase dashboard 437 or a named source
- Name stakeholders explicitly — not "the team", use first names from Granola/Slack
- Omit ⛔️ Blockers entirely if there are none — do not write "No blockers"
- Omit 💬 Discussion Items if nothing surfaced from Granola/Slack
- Never copy-paste the same update to both initiatives — different OKRs, different projects

---

## Step 3 — Present as ready-to-paste text

Show each update as a clean, copy-paste-ready block in chat — no surrounding explanation, no code fences, no commentary before or after the update text itself. Use this format:

---
**Update 1: AI Agent Activation — [paste below]**

[full update text, exactly as it will appear in Linear]

---
**Update 2: AI Agent Pipeline — [paste below]**

[full update text, exactly as it will appear in Linear]

---

Then ask Sara two questions:
1. Health status for each: `onTrack` / `atRisk` / `offTrack`?
2. Anything to add or change?

Do not post to Linear until Sara confirms.

---

## Step 4 — Post to Linear

Once confirmed, use `save_status_update` for each initiative:

```
type: "initiative"
initiative: [initiative ID]
health: [onTrack | atRisk | offTrack]
body: [confirmed update text in markdown]
```

Return the direct Linear URL for each posted update.

---

## Health status guidance

| Status | When to use |
|---|---|
| `onTrack` | Progress is on pace with the target date and OKR |
| `atRisk` | One or more blockers exist but the goal is still achievable |
| `offTrack` | The goal is unlikely to be met without a change in scope or timeline |

Default to Sara's judgment — suggest based on the blocker severity, but do not decide unilaterally.
