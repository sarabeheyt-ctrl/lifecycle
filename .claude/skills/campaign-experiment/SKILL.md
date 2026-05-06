---
name: campaign-experiment
description: >
  Documents a lifecycle campaign brief or experiment in the standard GRW Lifecycle
  format. Use when Sara wants to scope a new campaign, document a test, define
  variants, write a campaign brief, or capture results and learnings. Also triggers
  on "campaign brief", "experiment", "A/B test", "document this campaign",
  "define variants", "success metrics", "campaign hypothesis", "capture learnings",
  "experiment framework", "new campaign setup", "campaign doc".
---

# Campaign Experiment Framework

Generate a full campaign brief or experiment record in the standard GRW Lifecycle format. Ask Sara for campaign name, target bucket, and goal if not provided. Follow the structure below exactly.

---

## Step 0 — Research phase (always run before writing the brief)

Run all three sub-steps in parallel before writing anything.

### 0a — Cortex: account & configuration gap data

Use the Cortex MCP to pull real account data for the target bucket:

1. Call `get_instruction` to load the Cortex operating manual.
2. Navigate to `/topics/product/ai_agent` and `/topics/product/automated_interactions`.
3. Query `dim_accounts` joined with `dim_ai_agents` to pull:
   - Account count and ARR in the bucket
   - Distribution of `ai_agent_automation_rate_28` (median, average, % below threshold)
   - Configuration gap breakdown — what % of accounts are missing:
     - Knowledge sources (guidance articles, help center, website)
     - Channels enabled (chat, email, SMS)
     - Support actions enabled
     - Guidance opportunities reviewed
4. Query `accounts_history` for median automation rate trend over the last 60 days.
5. Query `dim_automate_interactions` for interaction volume trends.

### 0b — Notion: existing Candu and Customer.io IDs for this bucket

Search the Notion campaign registry for any existing campaigns targeting the same bucket:

1. Use `notion-search` to find pages in the Lifecycle Campaign Registry matching the bucket name.
2. For each match, fetch the page and extract:
   - **Customer.io Campaign ID** (CIO campaign ID and fly.customer.io link)
   - **Candu Segment IDs** (from the Segments table: Segment Name + Segment ID)
   - **Candu Content IDs** (from the Components table: Content ID)
   - **Candu Engagement IDs** (from the Components table: Engagement ID)
3. Compile a reference table of all existing IDs found:

**Existing IDs for this bucket (from Notion):**

| System | Name | ID | Link | Status |
|---|---|---|---|---|
| Customer.io | | | | Running / Paused / Draft |
| Candu Segment | | | | |
| Candu Component | | | | |
| Candu Engagement | | | | |

If no existing campaigns are found, note "No existing campaigns found for this bucket" and proceed.

### 0c — Content recommendations (based on 0a + 0b)

Combine the gap data from Cortex (0a) with the existing ID inventory from Notion (0b) to generate recommendations:

**Customer.io — recommended email topics (ranked by gap prevalence):**

| # | Email Topic | Trigger Condition | % of Bucket Affected | Existing CIO ID | Recommended CTA |
|---|---|---|---|---|---|
| 1 | | | | Reuse / New | |
| 2 | | | | Reuse / New | |
| 3 | | | | Reuse / New | |

**Candu — recommended component types:**

| Component Type | Placement | Segment (SLG/PLG/All) | Message Focus | Existing Content ID | Why (data signal) |
|---|---|---|---|---|---|
| Modal | | | | Reuse / New | |
| Inline content block | | | | Reuse / New | |
| Checklist | | | | Reuse / New | |

Mark each recommendation as **Reuse** (existing ID found) or **New** (needs to be created). The most common configuration gap becomes Email 1 and the primary Candu component. If the cohort is improving, prioritize advanced-optimization content over basic-setup content.

---

## Document structure

### 1. Context

Describe the lifecycle bucket this campaign targets:
- What defines an account in this bucket?
- How many accounts are currently in it, and what is their approximate ARR?
- What is the core problem or opportunity?
- Link to the Metabase dashboard for this bucket if known.

---

### 2. Target Audience

- Lifecycle bucket name
- Entry conditions (what triggers inclusion in this bucket)
- Link to the segment definition in Customer.io

---

### 3. Campaign Goal

> **Hypothesis:** [merchant segment] with [condition] are stuck at [root cause], not a [alternative cause]. The campaign addresses this through [channel(s)] that [mechanism of action].

Include:
- Confidence level (%)
- Current state data (e.g. median automation rate, account count)
- Conservative performance forecast:

**Customer.io forecast (30 days):**

| Metric | Assumption | Volume |
|---|---|---|
| Journey entries | — | |
| Open at least 1 email | % | |
| Click through | % | |
| Convert (primary action) | % | |

**Combined channel forecast (Email + Candu):**

| Channel | Converting accounts | Key outcome |
|---|---|---|
| Email sequence | | |
| Candu (additive) | | |
| **Combined** | | |

---

### 4. Entry & Exit Conditions

**Entry:** Define the exact conditions that trigger a merchant entering this campaign.

**Exit / suppression:** Define what causes a merchant to exit early (conversion event, manual suppression, overlap rule).

---

### 5. Campaign Design

#### Customer.io — Email Sequence

> Campaign ID: [CIO campaign ID — link to fly.customer.io]

**Step 1 — Map the sequence logic first.**
Before writing any copy, define the full sequence structure as a numbered list:
- What condition is checked at each step?
- What email fires if the condition is met (problem exists)?
- What happens if the condition is already resolved (skip logic)?
- What is the account goal at this step?

**Step 2 — Validate the sequence logic with Cortex before writing any copy.**
For each step in the sequence, query Cortex to verify the condition is worth a dedicated email:

1. Call `get_instruction` if not already loaded.
2. For each condition in the sequence, query `dim_accounts` joined with `dim_ai_agents` to calculate:
   - **Trigger rate** — what % of the bucket actually hits this condition (problem exists)?
   - **Skip rate** — what % of the bucket would skip this step (problem already solved)?
   - **Overlap** — do any two conditions apply to the same accounts? (signals that steps could be merged)
3. Produce a validation table before any copy is written:

| Step | Condition | Trigger rate | Skip rate | Verdict |
|---|---|---|---|---|
| Email 1 | | % | % | ✅ Keep / ⚠️ Merge / ❌ Remove |
| Email 2 | | % | % | |
| Email N | | % | % | |

**Verdict rules:**
- ✅ **Keep** — trigger rate ≥ 15% of bucket. Worth a dedicated email.
- ⚠️ **Merge** — trigger rate 5–15%. Consider combining with an adjacent step.
- ❌ **Remove** — trigger rate < 5%. Too few accounts to justify a step; fold into another email or drop.

Show this table to Sara and ask for confirmation before proceeding to copy. If Sara approves changes (merge/remove), update the sequence logic before moving to Step 3.

**Step 3 — Generate copy for each validated email using the Copywriter skill.**
For every email in the sequence, invoke the copywriter skill
(`.claude/skills/copywriter/SKILL.md`) with:
- The target lifecycle bucket
- The specific problem this email addresses (from the sequence logic above)
- The key signal that triggers this email (the condition from Step 1)
- The desired account action (the goal from Step 1)

Embed the full copywriter output for each email directly into the brief:

---

**Email [N] — [Email Name]**
- **Trigger condition:** [what must be true for this email to send]
- **Skip condition:** [what causes this step to be skipped]
- **Account goal:** [what action the merchant should take]

| Copy element | Content |
|---|---|
| Subject line (primary) | |
| Subject line (A/B variant) | |
| Preview text | |
| CTA label | |

> **Body copy:**
> [full email body here]

**Personalization notes:** [Liquid variables used + fallback values]

---

Repeat this block for every validated email in the sequence.

#### In-App — Candu

**Segments:**

| Segment Name | Segment ID | Description |
|---|---|---|
| | | |

**Components:**

| Component | Type | Content ID | Engagement Name | Engagement ID | Placement | Objective |
|---|---|---|---|---|---|---|
| | | | | | | |

#### Segmentation & Routing

Describe how SLG (AM/CSM) and PLG accounts are routed differently. Which Candu segments map to which components?

---

### 6. Experiment Setup (if A/B testing)

| Variant | Description | Split % |
|---|---|---|
| Control | | % |
| Variant A | | % |
| Variant B (if applicable) | | % |

**Primary metric** — the single number that determines a winner

**Secondary metrics** — supporting signals (open rate, click rate, feature adoption, demo booked)

**Guardrail metrics** — must not decline (e.g. unsubscribe rate, automation rate)

**Run parameters:**
- Start date:
- Minimum run duration:
- Minimum sample size per variant:
- Statistical significance threshold: 95% (default)

---

### 7. Execution Plan

**Rollout phases:**

| Cohort | Size | Accounts | Timeline | Goal |
|---|---|---|---|---|
| 1 — Debug | 2% | | Day 1–2 | Catch what breaks |
| 2 — Validate | 10% | | Day 3–4 | Confirm thresholds |
| 3 — Full launch | 88% | | Day 5+ | Full rollout |

**Overlap management:** List any campaigns running in parallel and how exclusions are handled.

**Campaigns to build before sunsetting overlap (if any):**

| Campaign | Trigger | Purpose |
|---|---|---|
| | | |

---

### 8. Results

*(Fill in after the campaign runs)*

| Metric | Target | Actual | Delta |
|---|---|---|---|
| Primary metric | | | |
| Secondary metric 1 | | | |
| Secondary metric 2 | | | |
| Guardrail metric | | | |

---

### 9. Learnings & Next Action

- What did we learn?
- Outcome: Roll out / Iterate / Abandon
- Does this update the campaign registry, signal registry, or Candu component inventory? If yes, what changes?

---

## Output

Do NOT return markdown for Sara to paste manually. Follow these steps in order:

### Step 1 — Show recommendations in chat
Display the content recommendations table (Step 0c) and the existing IDs table (Step 0b) in chat. Ask Sara to confirm or adjust before writing anything to Notion.

### Step 2 — Get the target Notion page
Ask Sara to paste the Notion campaign registry link if not already provided.
- Default registry: https://www.notion.so/2fd1ae2178f58027b159c1fffbe34332
- If Sara pastes a link to a **specific existing campaign page**, the new content goes there as a draft toggle (see Step 3b).
- If Sara pastes the **registry index**, create a new page (see Step 3a).

### Step 3a — New campaign: create a new page
Use `notion-create-pages` to create a new entry in the campaign registry database. Use the full document structure above as the page content. Return the link when done.

### Step 3b — Existing campaign: append a draft toggle
If the target page already has content (an existing campaign brief), do NOT overwrite it. Instead:

1. Use `notion-fetch` to read the existing page.
2. Use `notion-update-page` to append a collapsible toggle block at the bottom of the page with the title: **"🟡 Draft — [Campaign Name] — [Today's Date]"**
3. Place the entire new brief document **inside** the toggle, so it sits below the live content without disrupting it.
4. Return the link to the page with a note that the draft was added as a toggle at the bottom.

If Cortex MCP is unavailable, note this in chat, skip the data-driven recommendations, and still write to Notion with placeholder tables — do not silently skip either step.
