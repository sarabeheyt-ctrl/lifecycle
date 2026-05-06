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
  style="font-family: Arial, sans-serif; border-collapse: collapse;">
  <tr>
    <td style="padding: 24px; background-color: #ffffff;">
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
    { "name": "ctaUrl", "type": "string", "default": "#" },
    { "name": "headline", "type": "string", "default": "Your headline here" }
  ]
}
```
