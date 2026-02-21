---
name: visual-design-principles
description: Reference for visual hierarchy, typography scale, and color contrast in marketing design. Use when planning or reviewing any Figma design to make principled layout, type, and color decisions.
---

# Visual Design Principles

Structural principles for conversion-focused marketing design. Applied by `figma-designer` and `figma-design-reviewer`.

## Decision Guide

| Design decision | Reference |
|-----------------|-----------|
| Layout structure, section order, scan patterns | [layout-hierarchy.md](./references/layout-hierarchy.md) |
| Type scale, size, weight, typeface categories | [typography-scale.md](./references/typography-scale.md) |
| Color contrast, palette roles, CTA button rules | [color-contrast.md](./references/color-contrast.md) |

## When to Apply Each Reference

**[layout-hierarchy.md](./references/layout-hierarchy.md)** — Use when:
- Planning sections and visual weight for a new design
- Diagnosing hierarchy failures in a design review
- Choosing between layout patterns for landing pages vs. ads vs. posters
- Selecting frame dimensions for a given design type

**[typography-scale.md](./references/typography-scale.md)** — Use when:
- Selecting type size and weight for any text element
- Evaluating if the type scale has sufficient contrast between levels
- Choosing between typeface categories (serif vs. sans-serif vs. display)
- Verifying body text is legible at the intended size

**[color-contrast.md](./references/color-contrast.md)** — Use when:
- Verifying accessibility compliance before declaring a design publishable
- Diagnosing a contrast failure as a P1 issue in design review
- Choosing a CTA button color that stands out from the background
- Defining palette roles for a new design

## The Core Rule

Every visual element either reinforces the hierarchy or competes with it.

The hierarchy serves the marketing goal: guide the eye to the CTA.
Everything else is in service of that goal.

## Applying Captured Design Preferences

Before making any layout, type, or color decision, check `docs/insights/design-learnings/` for relevant preferences:

```
Grep: pattern="audience_segment:.*[audience keyword]" path=docs/insights/design-learnings/ -i=true
```

If a captured preference applies to the current audience segment and design type, apply it. Captured preferences override defaults from the references — they represent real user feedback, not generic best practices.
