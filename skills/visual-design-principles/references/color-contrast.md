# Color Contrast Reference

Color has two jobs in marketing design: support the visual hierarchy, and ensure legibility for every reader. Failing the second eliminates all the work done on the first.

## WCAG Contrast Ratios (Mandatory)

These are not just accessibility requirements — they are legibility requirements. Low contrast reduces conversion.

| Text type | Minimum ratio | Enhanced (AA+) |
|-----------|--------------|----------------|
| Normal text (< 18px regular or < 14px bold) | **4.5:1** | 7:1 |
| Large text (≥ 18px regular or ≥ 14px bold) | **3:1** | 4.5:1 |
| UI components, icons, input borders | **3:1** | — |
| Decorative elements (no information) | None | — |

**In practice:** Any headline that's 24px+ bold meets the large text threshold. Body copy at 16px must meet 4.5:1.

## Quick Contrast Heuristics

Exact luminance calculations are complex. Use these heuristics for fast decisions:

**Black (#000000):** Always sufficient against any color.

**White (#FFFFFF):** Sufficient when the background is dark enough. Safe colors for white text:
- Dark blues: #1E3A5F, #1D4ED8, #1E40AF and darker
- Dark greens: #166534, #15803D and darker
- Dark grays / blacks: #374151 and darker
- Deep purples: #4C1D95, #5B21B6 and darker
- Dark reds: #991B1B and darker

**When in doubt:** Flag for manual verification using a contrast checker. Provide the hex values in the design spec so the implementer can verify.

## CTA Button Contrast — Two Requirements

The CTA button must pass contrast in two directions simultaneously:

**1. Button vs. its background section** (user must see the button)
- Minimum 3:1 (the button is a UI component)
- Aim for much higher — the button should be the most visually distinct element on screen

**2. Button label text vs. button fill** (user must read the button)
- Minimum 4.5:1 for normal-sized button text
- Minimum 3:1 if button text is ≥18px or ≥14px bold

**Pre-verified safe combinations:**

| Button fill | Label text | Works on background | Result |
|-------------|-----------|---------------------|--------|
| #2563EB (blue) | #FFFFFF | White, light gray, light blue | ✓ |
| #1D4ED8 (deep blue) | #FFFFFF | Any light color | ✓ |
| #16A34A (green) | #FFFFFF | White, light gray | ✓ |
| #D97706 (amber) | #1A1A1A (dark) | White, light gray, light yellow | ✓ |
| #DC2626 (red) | #FFFFFF | White, light gray | ✓ |
| #7C3AED (purple) | #FFFFFF | White, light gray | ✓ |
| #F3F4F6 (light gray) | #6B7280 (mid gray) | White | ✗ (fails 4.5:1) |
| #FEF9C3 (light yellow) | #FBBF24 (yellow) | White | ✗ (fails on both) |
| #BFDBFE (light blue) | #3B82F6 (blue) | White | ✗ (borderline, avoid) |

## Palette Role Structure

Every design must have a defined role for each color. More than 5 colors without clear roles creates visual noise that reduces hierarchy.

| Role | Count | Purpose | Where it appears |
|------|-------|---------|-----------------|
| **Primary** | 1 color | Brand identity, most important interactive element | CTA button, primary links, key accents |
| **Secondary** | 1 color | Supporting elements, section accents, secondary CTAs | Section backgrounds, secondary buttons, icons |
| **Neutral** | 2–3 shades | Text, borders, backgrounds | Body text, subheadings, dividers, page background |
| **Semantic** | Optional (3) | Error, success, warning | Form validation only — don't use in marketing design unless forms are present |

**Maximum colors in a marketing design: 5** (1 primary + 1 secondary + 3 neutrals). More creates visual noise.

**The neutral trap:** Neutral colors are not "no color" — they carry significant weight in hierarchy. Dark neutrals (#1A1A1A) for body text. Mid neutrals (#6B7280) for secondary text and captions. Light neutrals (#F3F4F6) for section backgrounds and dividers.

## Color and Hierarchy

Color should reinforce the visual hierarchy established by size and weight, not create a competing hierarchy.

**Right:** Primary color on the most important element (CTA). Secondary color on secondary elements. Neutrals on supporting content.

**Wrong:** Using the primary brand color on a decorative background, then a neutral color on the CTA. The button disappears.

**The hierarchy rule for color:** The most important element should have the highest color contrast against its immediate background. If the CTA button doesn't have the highest contrast element on screen, the palette is fighting the hierarchy.

## Common Color Mistakes

| Mistake | Fix |
|---------|-----|
| CTA button is a lighter shade of the brand color | Use the darkest/most saturated variant of the brand color for the CTA |
| Text in a colored hero section fails contrast | Switch to white or very dark text; add a semi-transparent overlay on background images |
| Five different accent colors with no roles | Pick one accent (primary) and one supporting (secondary); make everything else neutral |
| Logo color used as CTA button color | Check if the logo color actually passes 4.5:1 for button label text — many brand colors don't |
| Photo background with text overlay | Add a dark or light gradient overlay with ≥50% opacity before placing text |
