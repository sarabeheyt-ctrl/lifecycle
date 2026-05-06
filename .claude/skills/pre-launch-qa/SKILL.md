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

Actively audit a campaign before launch by pulling real data from Customer.io,
Notion, and live URLs. Do not ask Sara to manually confirm anything that can be
verified programmatically.

Ask Sara for:
- Customer.io campaign ID (or name)
- Notion campaign registry page link

---

## Step 0 — Pull all campaign data (run in parallel)

Before any checks, fetch the source of truth for this campaign from all systems simultaneously:

### 0a — Customer.io MCP
Use the Customer.io MCP to pull:
- Full campaign/journey definition (steps, branches, wait logic, trigger conditions)
- All email content blocks (subject lines, body, CTAs, Liquid variables)
- Segment definitions attached to the campaign (entry segment, suppression segments)
- Sending limits and frequency cap settings
- Campaign status (draft / active / paused)

### 0b — Notion registry
Use `notion-fetch` on the campaign page to extract:
- Customer.io Campaign ID
- Candu Segment IDs (all segments in the Segments table)
- Candu Content IDs (all components in the Components table)
- Candu Engagement IDs (all components in the Components table)
- Linear issue link
- Lifecycle bucket tag

### 0c — Extract all URLs
From the CIO email content pulled in 0a, extract every URL found in:
- CTA buttons and links
- Image links
- Unsubscribe / footer links

---

## Step 1 — Links

For every URL extracted in Step 0c, fetch it and verify:

| URL | Destination | Status code | UTM source | UTM medium | UTM campaign | UTM content | Result |
|---|---|---|---|---|---|---|---|
| | | 200 / 301 / 404 / error | | | | | PASS / FAIL |

**Checks:**
- Status code is 200 or a clean redirect (301/302) — broken links (404, 5xx, timeout) are BLOCKING
- `utm_source` is present on every CTA link
- `utm_medium` is present on every CTA link
- `utm_campaign` matches the CIO campaign ID or slug
- `utm_content` identifies the specific email step (e.g. `email_1_knowledge_gap`)
- Unsubscribe link is present and resolves correctly

---

## Step 2 — Segments

For each segment attached to the campaign, verify via CIO MCP:

| Segment | ID | Size | Entry / Suppression | Naming convention | Result |
|---|---|---|---|---|---|
| | | N accounts | | `[Bucket] — [Campaign] — [Variant]` | PASS / FAIL |

**Checks:**
- Segment exists and has accounts (size > 0 is non-blocking; size = 0 is BLOCKING)
- Entry segment is scoped to the correct lifecycle bucket (cross-check against Foundation)
- Suppression segments are configured for frequency cap and overlap management
- Segment name follows convention: `[Bucket] — [Campaign Name] — [Variant]`
- Holdout group segment is configured (10–20% of audience)

---

## Step 3 — Flow & Triggers

Using the journey definition from CIO MCP, walk every step and verify:

| Step | Type | Condition | Timeout set | Fallback branch | Result |
|---|---|---|---|---|---|
| | Wait / Email / Branch / Exit | | Yes / No / N/A | Yes / No / N/A | PASS / FAIL |

**Checks:**
- Entry trigger conditions are correctly defined and match the bucket criteria from the Foundation Reference
- Exit/conversion gate fires immediately when the merchant converts (does not wait for next step)
- Re-entry rules are explicitly configured
- Every ConditionalWait has a timeout — no merchant can be stuck indefinitely
- Every conditional branch (Yes/No) has both paths defined — no dead ends
- Wait durations are appropriate for the campaign cadence (flag anything under 12h or over 14 days as unusual)

---

## Step 4 — CTAs

For every CTA found in the email content:

| Email step | CTA label | Destination | UTMs present | Label follows rule | Result |
|---|---|---|---|---|---|
| | | | Yes / No | Yes / No | PASS / FAIL |

**CTA label rule:** Must be verb + object describing the action, not the destination.
- ✅ `Add your first knowledge source` / `Book a setup call` / `Review your AI Agent gaps`
- ❌ `Click here` / `Learn more` / `Get started` / `Find out how`

---

## Step 5 — Copy

For each email in the sequence, run the copy through these checks using the content pulled from CIO:

| Email | Subject line ≤50 chars | Preview text present | One CTA only | No forbidden phrases | Liquid fallbacks | Result |
|---|---|---|---|---|---|---|
| | Yes / No | Yes / No | Yes / No | Yes / No | Yes / No | PASS / FAIL |

**Forbidden phrases** (flag any of these as FAIL):
- "unlock the full power", "supercharge", "game-changing", "revolutionary"
- "Let us know if you have questions"
- "Click here", "Learn more" (as CTA labels)
- Internal jargon: "lifecycle bucket", "PLG/SLG", "AAO rate", "automation cohort"

**Liquid checks:**
- Every `{{ variable }}` has a `| default:` fallback
- Subject line does not contain unresolved Liquid (e.g. `{{ account.name }}` without fallback)

---

## Step 6 — Interaction Labels & Candu IDs

Cross-check Candu IDs from Notion (Step 0b) against naming conventions:

| Component | Content ID | Engagement ID | Segment ID | Engagement label | Label follows convention | Result |
|---|---|---|---|---|---|---|
| | | | | | `[Bucket] — [Campaign] — [Action]` | PASS / FAIL |

**Checks:**
- All Content IDs, Engagement IDs, and Segment IDs are populated in the Notion registry (blank = BLOCKING)
- Engagement/interaction labels follow convention: `[Bucket] — [Campaign Name] — [Action]`
- Labels are consistent across all components in this campaign (no variations in spelling or casing)
- Customer.io Campaign ID is logged in the Notion registry (blank = BLOCKING)

---

## Step 7 — Registry Completeness

Final check: everything needed for Metabase dashboard 437 to track this campaign.

| Item | Present in Notion registry | Result |
|---|---|---|
| Customer.io Campaign ID | Yes / No | PASS / FAIL |
| Candu Segment IDs (all) | Yes / No | PASS / FAIL |
| Candu Content IDs (all) | Yes / No | PASS / FAIL |
| Candu Engagement IDs (all) | Yes / No | PASS / FAIL |
| Linear issue linked | Yes / No | PASS / FAIL |
| Lifecycle bucket tag | Yes / No | PASS / FAIL |

---

## Output

Return in this order:

### 1. Summary scorecard
| Section | Checks passed | Checks failed | Blocking failures |
|---|---|---|---|
| Links | / | / | |
| Segments | / | / | |
| Flow & Triggers | / | / | |
| CTAs | / | / | |
| Copy | / | / | |
| Interaction Labels & Candu IDs | / | / | |
| Registry Completeness | / | / | |

### 2. Blocking issues (must fix before launch)
List every blocking failure with exactly what needs to change and where.

### 3. Non-blocking issues (fix soon, won't hold launch)
List with recommended fix for each.

### 4. Missing IDs
Focused table of every blank ID in the Notion registry:

| System | What's missing | Where to add it |
|---|---|---|
| Customer.io | Campaign ID | Notion registry → Campaign section |
| Candu | Content ID for [component] | Notion registry → Components table |
| Candu | Engagement ID for [component] | Notion registry → Components table |

### 5. Verdict
`✅ READY TO LAUNCH` or `❌ NOT READY — [N] blocking issues`

**Auto-blocking rules:**
- Any broken link (404 / 5xx)
- Any segment with 0 accounts
- Any blank CIO Campaign ID or Candu ID in the Notion registry
- Any CTA without UTM parameters
- Any ConditionalWait without a timeout
- Any conditional branch with a missing path
