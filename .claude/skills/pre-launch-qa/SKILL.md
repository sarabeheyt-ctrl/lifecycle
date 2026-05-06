---
name: pre-launch-qa
description: >
  Runs a pre-launch QA checklist before any Customer.io campaign goes live.
  Use when Sara asks to QA a campaign, check if a campaign is ready to launch,
  review a campaign before activation, or verify campaign setup. Also triggers
  on "is this campaign ready", "QA this campaign", "pre-launch check",
  "campaign review", "ready to launch", "check before I activate".
---

# Pre-Launch QA Checklist

Run a full pre-launch audit before any campaign goes live. This covers journey
logic, copy, audience, tracking, and registry completeness. Missing tracking
information (UTM, Candu IDs, CIO campaign ID) makes the campaign invisible to
reporting — these are blocking issues.

Ask Sara for the campaign name and Notion campaign page link if not provided.

---

## Step 0 — Pull campaign data from Notion

Before running any checks, fetch the campaign's Notion page from the registry:

1. Use `notion-fetch` on the campaign page link provided by Sara.
2. Extract all available IDs from the page:
   - Customer.io Campaign ID (and fly.customer.io link)
   - Candu Segment IDs (Segment Name + Segment ID from the Segments table)
   - Candu Content IDs (Content ID from the Components table)
   - Candu Engagement IDs (Engagement ID from the Components table)
3. Note which IDs are present and which are blank — blanks are automatic FAIL items in the Tracking section below.

---

## Checks

### Section A — Campaign Registry Completeness
These are the tracking checks. Every blank ID means the campaign cannot be reported on correctly in Metabase dashboard 437. All are BLOCKING.

| Check | Status | Notes |
|---|---|---|
| Campaign has a Notion registry entry | PASS / FAIL | |
| Customer.io Campaign ID is logged in Notion | PASS / FAIL | |
| Candu Segment IDs are logged in Notion (all segments) | PASS / FAIL | |
| Candu Content IDs are logged in Notion (all components) | PASS / FAIL | |
| Candu Engagement IDs are logged in Notion (all components) | PASS / FAIL | |
| Campaign is tagged with the correct lifecycle bucket label in CIO | PASS / FAIL | |
| Linear issue is linked in the Notion registry entry | PASS / FAIL | |

---

### Section B — UTM & Interaction Labels
Missing UTMs mean clicks can't be attributed. Missing interaction labels mean Candu events won't surface in reporting.

| Check | Status | Notes |
|---|---|---|
| All CTA links have `utm_source` | PASS / FAIL | |
| All CTA links have `utm_medium` | PASS / FAIL | |
| All CTA links have `utm_campaign` matching the CIO campaign ID or name | PASS / FAIL | |
| All CTA links have `utm_content` identifying the specific email step | PASS / FAIL | |
| Candu engagement events are named following convention: `[Bucket] — [Campaign] — [Action]` | PASS / FAIL | |
| Candu interaction labels are consistent across all components in this campaign | PASS / FAIL | |

---

### Section C — Entry & Exit Conditions

| Check | Status | Notes |
|---|---|---|
| Entry conditions scoped to correct lifecycle bucket | PASS / FAIL | |
| Exit/conversion gate configured (immediate exit on convert) | PASS / FAIL | |
| Re-entry rules explicitly set | PASS / FAIL | |

---

### Section D — Journey Logic

| Check | Status | Notes |
|---|---|---|
| Wait logic correct for campaign cadence | PASS / FAIL | |
| Every ConditionalWait has a timeout | PASS / FAIL | |
| Every conditional branch has a fallback path | PASS / FAIL | |

---

### Section E — Copy & Personalization

| Check | Status | Notes |
|---|---|---|
| All Liquid tokens tested and resolve correctly | PASS / FAIL | |
| Every Liquid variable has a fallback value | PASS / FAIL | |
| CTA links are live and tracked | PASS / FAIL | |
| Fallback copy exists for null signal values | PASS / FAIL | |

---

### Section F — Audience & Targeting

| Check | Status | Notes |
|---|---|---|
| Segment named: `[Bucket] — [Campaign Name] — [Variant]` | PASS / FAIL | |
| Holdout group configured (10–20%) | PASS / FAIL | |
| Audience size sufficient for statistical significance | PASS / FAIL | |
| Frequency caps respected | PASS / FAIL | |

---

## Output

After running all checks, return:

1. **Full results table** — all sections with PASS/FAIL/N/A per check and notes.

2. **Missing IDs summary** — a focused list of every blank ID that needs to be filled in the Notion registry before launch:

| System | What's missing | Where to add it |
|---|---|---|
| Customer.io | Campaign ID | Notion registry → Campaign section |
| Candu | Content ID for [component name] | Notion registry → Components table |
| Candu | Engagement ID for [component name] | Notion registry → Components table |
| Candu | Segment ID for [segment name] | Notion registry → Segments table |

3. **Blocking issues** — every FAIL that must be resolved before launch.

4. **Non-blocking issues** — FAILs that should be fixed but won't hold launch.

5. **Verdict**: `READY TO LAUNCH` / `NOT READY — [N] blocking issues`

If any Section A or Section B item fails, the verdict is automatically NOT READY regardless of everything else. Reporting integrity is non-negotiable.
