---
name: design-studio
description: >
  Builds or fixes Customer.io Design Studio HTML email components. Use when Sara
  asks to create an email component, fix a "props is not defined" error, build a
  CTA block, header, banner, or any HTML for Customer.io Design Studio.
  Also triggers on "build a component", "fix this component", "Design Studio",
  "CIO component", "email template", "props error", "#root", "inline styles",
  "visual mode error".
---

# Design Studio Agent

Generate or fix Customer.io Design Studio HTML components that render correctly in visual mode without errors.

## Non-negotiable rules — every component must follow all of these

1. **`id="root"` on the outermost element** — the top-level wrapper must have `id="root"`, no exceptions
2. **Inline styles only** — no `<style>` blocks, no `<link>` tags, no class-based styling whatsoever
3. **Table-based layout** — use `<table>`, `<tr>`, `<td>` for all layout and spacing; no flexbox, no grid, no `<div>` for layout
4. **No `x-` components** — do not use `x-component`, `x-slot`, or any custom element tags
5. **No Liquid `{% %}` blocks** — no `{% if %}`, `{% for %}`, `{% assign %}` or any Liquid logic tags; Design Studio does not support them
6. **All props must be declared** — every `{{props.variableName}}` referenced in the HTML must have a matching entry in the props definition; missing declarations cause `props is not defined` errors

## CTA & link rules

1. **One CTA button per email** — one primary button only. Additional resource links in body copy are acceptable as inline text, but not as buttons.
2. **UTMs required on every CTA** — all four parameters must be present on every `href`: `utm_source`, `utm_medium`, `utm_campaign` (must include the specific email step, e.g. `underperformers_email1` not just `underperformers`), `utm_content` (the specific action, e.g. `add-knowledge`).
3. **Consistent `utm_medium`** — pick one value across all emails in a campaign (`customerio` or `email`) and never mix them.
4. **Liquid variables in CTA URLs require fallbacks** — any `{{customer.X}}` inside an `href` must have a `| default:`. If the variable resolves to null, the URL breaks silently.
5. **Fallback destination is always the merchant homepage** — when building a fallback for a personalized URL, the fallback must resolve to a valid page, not `#` or an empty string. Use the pattern:

```
https://{{customer.account_gorgias_subdomain | default: 'app'}}.gorgias.com
```

If the full path can't be constructed safely (e.g. the shop name is also required and could be null), fall back to the homepage rather than a broken deep-link:

```html
href="https://{{customer.account_gorgias_subdomain | default: 'app'}}.gorgias.com/app/ai-agent/shopify/{{customer.account_ai_agent_most_configured_shop_name | default: ''}}/knowledge/sources/?utm_source=plg&utm_medium=customerio&utm_campaign=[slug]_email[N]&utm_content=[action]"
```

6. **Recipient field must be set explicitly** — every email action must have `{{customer.email}}` as the recipient. An empty recipient field means the email does not send. Do not rely on a campaign-level default.

---

## Copy rules

Apply these when writing or reviewing any email content built in Design Studio:

1. **Subject line ≤50 characters** — count with Liquid fallbacks resolved. `{{customer.first_name | default: 'Hey'}}` counts as "Hey" (3 chars), not the full expression. If the resolved subject exceeds 50 chars, shorten the static text.
2. **Preview text / preheader must be unique per email** — never reuse the same preheader across A/B variants of the same step. Each variant must have a distinct preheader that complements its subject line.
3. **Every `{{customer.X}}` in the body needs a `| default:`** — a missing fallback renders as blank and breaks sentences mid-copy. Common patterns:
   - `{{customer.first_name | default: "there"}}`
   - `{{customer.account_name | default: "your store"}}`
   - `{{customer.account_ai_agent_automation_rate_28 | times: 100 | round | default: 0}}%`
4. **No forbidden phrases** — do not use: "unlock the full power", "supercharge", "game-changing", "revolutionary", "Let us know if you have questions", "Click here", "Learn more" as CTA labels, or any internal jargon (lifecycle bucket, PLG/SLG, AAO rate, automation cohort).
5. **CTA label must be verb + object** — describes the action, not the destination. ✅ "Add your first knowledge source" ❌ "Get started" / "Learn more" / "Click here".

---

## Gorgias brand tokens (Axiom design system)

Always use these tokens when building email components. Do not invent hex values or fonts.

**Colors (verified from Figma Axiom homepage reference):**

| Token | Hex | Use |
|---|---|---|
| Primary text | `#1a1e23` | All headings, button labels, body on white |
| Secondary text | `#5c6370` | Supporting copy, captions |
| Accent coral | `#ff9780` | Highlights, inline accents |
| Border / divider | `#e8e3e1` | Card borders, horizontal rules |
| Surface light | `#fafafa` | Section backgrounds, alternating rows |
| Card background | `#ffffff` | Main content background |
| Dark section bg | `#000000` | Footer-style dark sections |
| Success | `#32c898` | Positive metrics, completion states |
| Error | `#ff425d` | Warning/alert states |

**Typography (verified from Figma — font is Inter Tight, not Inter):**

| Element | Font | Weight | Size | Color |
|---|---|---|---|---|
| H1 / Hero headline | Inter Tight | Medium (500) | 60px | `#1a1e23` or `#ffffff` on dark |
| H2 / Section headline | Inter Tight | Regular (400) | 52px | `#1a1e23` |
| H3 / Sub-heading | Inter Tight | Regular (400) | 28px | `#1a1e23` |
| Eyebrow / label | Inter Tight | Medium (500) | 14px | `#1a1e23` |
| Body large | Inter Tight | Regular (400) | 18px | `#1a1e23` |
| Body default | Inter Tight | Regular (400) | 14px | `#1a1e23` |
| Button label | Inter Tight | Medium (500) | 16px | see button styles |

**Email font stack:** `'Inter Tight', Inter, Arial, sans-serif`

**Spacing scale:**

| Token | px |
|---|---|
| xxxs | 4 |
| xxs | 6 |
| xs | 8 |
| xl | 32 |
| xxxl | 64 |

**Button styles (verified from Figma):**

Primary button (on white/light background):
```html
style="background-color: #1a1e23; color: #ffffff; font-family: 'Inter Tight', Inter, Arial, sans-serif;
       font-size: 16px; font-weight: 500; padding: 12px 24px; border-radius: 8px;
       text-decoration: none; display: inline-block;"
```

Primary button (on dark/black background):
```html
style="background-color: #ffffff; color: #1a1e23; font-family: 'Inter Tight', Inter, Arial, sans-serif;
       font-size: 16px; font-weight: 500; padding: 12px 24px; border-radius: 8px;
       text-decoration: none; display: inline-block;"
```

---

## CIO attribute lookup

Before using any `{{customer.X}}` Liquid variable in a component, verify the attribute name is correct:

1. **Check `.claude/context/cio-attributes.csv`** — this file lists all active CIO people attributes. Find the exact attribute name in the `Name` column.
2. **Verify with Cortex** — call `get_instruction`, navigate to the relevant topic (e.g. `/topics/product/ai_agent`), then query `dim_accounts` to confirm the column exists and check its values/type.
3. **Only use attributes that appear in both sources.** If an attribute is in the CSV but cannot be verified in Cortex, note it as unverified and use a safe fallback.
4. **Always add `| default:`** — see Copy rules above. No exceptions.

Common verified attributes:

| Use case | CIO attribute | Safe fallback |
|---|---|---|
| Personalized greeting | `customer.first_name` | `"there"` |
| Store/account name | `customer.account_gorgias_subdomain` | `"your store"` |
| Automation rate (AI Agent buckets) | `customer.account_ai_agent_automation_rate_28` | `0` |
| Automation rate (Automate buckets) | `customer.account_aao_automation_rate_28` | `0` |
| AI Agent knowledge shop | `customer.account_ai_agent_most_configured_shop_name` | `""` |
| Subdomain (for URLs) | `customer.account_gorgias_subdomain` | `"app"` |
| CSM first name | `customer.account_csm_first_name` | `"your Customer Success Manager"` |
| CSM calendar link | `customer.account_csm_calendar_link` | `"https://app.gorgias.com"` |

---

## Fixing a "props is not defined" error

1. Read every `{{props.X}}` reference in the HTML
2. Check the component's props definition — is each one declared?
3. Add any missing props with a sensible default value
4. Return the corrected component with all props declared and defaults set

## Output format

Always return three things:

1. **Complete HTML** — ready to paste into Design Studio, no placeholders
2. **Props definition** — a list of every prop, its type, and its default value
3. **Change notes** — a short list of what was fixed or applied

## Reference structure

```html
<table id="root" width="100%" cellpadding="0" cellspacing="0" border="0"
  style="font-family: 'Inter Tight', Inter, Arial, sans-serif; border-collapse: collapse; background-color: #ffffff;">
  <tr>
    <td style="padding: 32px; color: #1a1e23;">
      <!-- content -->
    </td>
  </tr>
</table>
```

## Props definition format

```json
{
  "props": [
    { "name": "ctaLabel", "type": "string", "default": "Learn more" },
    { "name": "ctaUrl", "type": "string", "default": "https://app.gorgias.com" },
    { "name": "headline", "type": "string", "default": "Your headline here" }
  ]
}
```
