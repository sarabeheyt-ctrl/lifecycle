---
name: lifecycle-foundation
description: >
  Reference for the GRW Lifecycle system: all 12 lifecycle buckets, entry/exit
  criteria, overlay flags, account segmentation rules, and progression logic.
  Use this whenever any skill or question requires knowing which bucket an account
  is in, what the entry criteria are, how accounts progress through the lifecycle,
  or what the overlay flags mean. Also triggers on "what bucket", "lifecycle
  buckets", "bucket definition", "segmentation", "entry criteria", "eligibility",
  "overlay flag", "is_at_risk", "progression", "lifecycle foundation".
---

# Lifecycle Foundation Reference

This is the authoritative reference for the GRW Lifecycle system. Read this file
whenever you need to answer questions about bucket definitions, eligibility, or
progression logic. Source of truth: Lifecycle Journey Foundations (Notion).
Metabase dashboard: https://prod.metabase.gorgias-decision-engine.com/dashboard/437

---

## Account Type Definitions

### Helpdesk-Only
- `account_status` = `customer`
- `account_plan` ≠ `free`
- `account_first_activated_date` exists
- `account_automation_addon_subscription_status` does not exist OR ≠ `current_subscriber`
- `account_enabled_ai_agent` = false

### Automate Subscription (no AI Agent)
- `account_status` = `customer`
- `account_plan` ≠ `free`
- `account_automation_addon_subscription_status` = `current_subscriber`
- `account_automation_cancel_at` does not exist
- `account_enabled_ai_agent` = false
- `ecommerce_platform` = `shopify`

### AI Agent Enabled
- `account_status` = `customer`
- `account_plan` ≠ `free`
- `account_first_activated_date` exists
- `account_automation_addon_subscription_status` = `current_subscriber`
- `account_enabled_ai_agent` = true
- `account_automation_cancel_at` does not exist
- `ecommerce_platform` = `shopify`

---

## Eligibility & GMV Bands

### PLG (self-serve)
- `fixed_gmv_band` = `SMB (<$5M)`, OR
- `fixed_gmv_band` = `Commercial ($5-30M)` AND `vitally_custom_commercialprioritylevelq12026` = `Low`

### SLG (sales-assisted)
- `fixed_gmv_band` = `Commercial ($5-30M)` AND `vitally_custom_commercialprioritylevelq12026` = `High`
- CSM: `account_ai_agent_optimization_cohort` is Segment 1–7
- AM: `account_total_interactions_28` > 600, `account_is_customer_on_ai_agent_usd6_plan` = false, `account_ai_agent_optimization_cohort` does not exist

### User targeting
- `gorgias_persona_seniority` = senior OR `role` = admin
- `automate_setup_owner` = true

### Hard exclusions (all campaigns)
- Partners/agencies: `user_partner_type` must not exist
- Scheduled cancellations: `helpdesk_cancel_at` is a future timestamp
- Users with active sales engagement: `user_journey_status` ≠ `no active engagement`

---

## The 12 Lifecycle Buckets

### Tier 1 — Helpdesk Only

| Bucket | Definition | Entry Criteria |
|---|---|---|
| **Helpdesk Dormant** | Paying helpdesk-only customers with zero ticket interactions in the last 28 days, activated 60+ days ago. Generating no product value. | `Helpdesk Only` + `total_interactions_28` = 0 + `first_activated_date` ≥ 60 days ago |
| **Helpdesk Early Stage** | Helpdesk-only customers activated within the last 28 days. In the critical first-impression window. | `Helpdesk Only` + `total_interactions_28` < 500 + `first_activated_date` < 28 days ago |
| **Helpdesk Low Volume** | Helpdesk-only customers with <500 interactions in 28 days. Activated 28+ days ago. | `Helpdesk Only` + `first_activated_date` ≥ 28 days ago + `total_interactions_28` < 500 |
| **Helpdesk High Volume** | Helpdesk-only customers with 500+ interactions in 28 days. Activated 28+ days ago. Strong engagement, prime cross-sell target. | `Helpdesk Only` + `first_activated_date` ≥ 28 days ago + `total_interactions_28` > 500 |

### Tier 2 — Automate Subscription (no AI Agent)

| Bucket | Definition | Entry Criteria |
|---|---|---|
| **Automate Dormant** | Automate subscribers with zero automation activity (`aao_automation_rate_28` = 0 or null) in 28 days, subscribed 60+ days ago. Paying but not using. | `Automate subscription` + `aao_automation_rate_28` IS NULL or = 0 + `automate_subscribed_at` ≥ 60 days ago |
| **Automate Early Stage** | Automate subscribers within 28 days of subscribing. Critical activation window. | `Automate subscription` + `automate_subscribed_at` < 28 days ago |
| **Automate Underperforming** | Automate subscribers with AAO automation rate <20% (or null). Subscribed 28+ days ago. Not hitting meaningful automation levels. | `Automate subscription` + `automate_subscribed_at` ≥ 28 days ago + `aao_automation_rate_28` < 0.20 |
| **Automate Performing** | Automate subscribers with AAO automation rate ≥20%. Subscribed 28+ days ago. Achieving meaningful automation with Flows. | `Automate subscription` + `automate_subscribed_at` ≥ 28 days ago + `aao_automation_rate_28` ≥ 0.20 |

### Tier 3 — AI Agent Enabled

| Bucket | Definition | Entry Criteria |
|---|---|---|
| **AI Agent Early Stage** | AI Agent enabled within the last 28 days. Critical configuration window where knowledge base quality and channel setup determine performance. | `AI agent enabled` + `automate_subscribed_at` < 28 days ago |
| **AI Agent Underperforming** | AI Agent enabled 28+ days ago with automation rate <14% (or null). Setup gaps — knowledge base, channel config, or intent coverage — are limiting performance. | `AI agent enabled` + `ai_support_agent_first_enabled_date` ≥ 28 days ago + `ai_agent_automation_rate_28` < 0.14 OR IS NULL |
| **AI Agent Growing** | AI Agent enabled 28+ days ago with automation rate between 14% and 28%. Solid performance with room to grow toward Champion. | `AI agent enabled` + `ai_agent_automation_rate_28` ≥ 0.14 AND < 0.28 |
| **AI Agent Champion** | AI Agent enabled with automation rate ≥28%. Highest-performing AI customers. Full product adoption. Shopping Assistant tracked separately via `has_shopping_assistant`. | `AI agent enabled` + `ai_agent_automation_rate_28` ≥ 0.28 |

---

## Progression Logic

**Horizontal progression** — improving within the same product tier (reducing churn risk, increasing engagement volume).

**Vertical progression** — graduating to the next product tier (Helpdesk → Automate → AI Agent).

The North Star for each tier:
- Helpdesk: increase interaction volume and reduce dormancy
- Automate: increase AAO automation rate toward 20%
- AI Agent: increase AI automation rate toward 14% (Underperforming → Growing) and 28% (Growing → Champion)

---

## Overlay Flags

Overlay flags apply **on top of** bucket classification. They never override a customer's bucket — they modify campaign urgency, messaging tone, or trigger additional workflows.

**Always query Cortex for live counts before reporting these numbers.** The counts below are stale reference points only — do not cite them. Instead:

1. Call `get_instruction` if Cortex is not already loaded.
2. Run this query against `dim_accounts` to get live counts for all three flags:

```sql
SELECT
  COUNTIF(churn_risk_score >= 70) AS is_at_risk_count,
  COUNTIF(
    automate_subscription_status = 'addon_churn'
    OR (is_ai_agent_support_enabled = false AND is_ai_agent_support_ever_enabled = true)
  ) AS has_churned_from_higher_tier_count,
  COUNTIF(is_ai_sales_agent_enabled = true) AS has_shopping_assistant_count
FROM `your_project.your_dataset.dim_accounts`
WHERE status IN ('customer')
```

3. Verify the exact column names against `dim_accounts` children in Cortex before executing.
4. Report live counts in your answer. If Cortex is unavailable, use the reference counts below and flag them as approximate.

| Flag | Definition | Effect | Reference count (may be stale) |
|---|---|---|---|
| `is_at_risk` | `churn_risk_score` ≥ 70 | Increases urgency, triggers CSM escalation | ~1,067 |
| `has_churned_from_higher_tier` | Previously had Automate or AI Agent, now on lower tier | Switches to win-back positioning | ~2,376 |
| `has_shopping_assistant` | `is_ai_sales_agent_enabled` = true | Modifies Champion campaigns: with it → advocacy, without it → cross-sell target | ~1,488 |

---

## Additional Buckets (outside main 12)

| Bucket | Criteria | Campaign approach |
|---|---|---|
| **Churned (<90 days)** | `status` = `churn` + churned within last 90 days | Tiered win-back sequence by product tier |
| **Helpdesk Trial / Pre-Activation** | `status` = `trial`, not yet activated | Trial activation series (highest-leverage intervention in the lifecycle) |

---

## Campaign Registry

The live campaign registry lives in Notion:
https://www.notion.so/2fd1ae2178f58027b159c1fffbe34332

Each campaign entry maps to: target bucket → objective → target bucket after conversion.
