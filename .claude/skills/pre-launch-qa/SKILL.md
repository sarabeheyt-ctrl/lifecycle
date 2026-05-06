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

Run a structured pass/fail audit on a Customer.io campaign before it goes live. Ask Sara for the campaign name/ID and any relevant setup details if not provided.

## Checks to run

### Entry & Exit Conditions
- Entry conditions are clearly defined and scoped to the correct lifecycle bucket
- Exit/conversion gate is configured — a merchant who converts exits the journey immediately
- Re-entry rules are explicitly set (can a merchant re-enter? after how long?)

### Journey Logic
- Wait logic is correct and appropriate for the campaign cadence
- Every ConditionalWait has a timeout — no merchant should be stuck indefinitely
- Every conditional branch has a fallback path

### Copy & Personalization
- All Liquid personalization tokens are tested and resolve correctly
- Every Liquid variable has a fallback value (e.g. `{{ account.name | default: "there" }}`)
- CTA links are correct, live, and tracked
- Fallback copy exists for empty or null signal values

### Audience & Targeting
- Segment is named following the convention: `[Bucket] — [Campaign Name] — [Variant]`
- Holdout group is configured (recommended: 10–20%)
- Audience size is large enough to reach statistical significance

### Operations
- Linear issue is linked to this campaign
- Campaign is tagged with the correct lifecycle bucket label
- Sending limits and frequency caps are respected

## Output format

Return a table with every check, then a summary:

| Check | Status | Notes |
|---|---|---|
| Entry conditions defined | PASS / FAIL | |
| Exit/conversion gate | PASS / FAIL | |
| Re-entry rules set | PASS / FAIL | |
| Wait logic correct | PASS / FAIL | |
| ConditionalWait timeouts | PASS / FAIL | |
| Fallback branches | PASS / FAIL | |
| Liquid tokens tested | PASS / FAIL | |
| Liquid fallback values | PASS / FAIL | |
| CTA links live | PASS / FAIL | |
| Fallback copy present | PASS / FAIL | |
| Segment naming convention | PASS / FAIL | |
| Holdout group configured | PASS / FAIL | |
| Audience size sufficient | PASS / FAIL | |
| Linear issue linked | PASS / FAIL | |
| Lifecycle bucket tag | PASS / FAIL | |
| Frequency caps respected | PASS / FAIL | |

Then list:
- **BLOCKING** — must fix before launch
- **NON-BLOCKING** — should fix but won't hold launch
- **Verdict**: READY TO LAUNCH / NOT READY
