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

Describe the email sequence step by step. For each step:
- What condition is checked?
- What email is sent if the condition is met?
- What happens if the condition is already resolved (skip)?
- What is the account goal at this step?

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

Return the completed document in markdown, ready to paste into Notion. Leave results and learnings sections blank with the table structure in place if the campaign hasn't launched yet. Always include the Candu segment table and component table — fill with placeholders if IDs aren't known yet.
