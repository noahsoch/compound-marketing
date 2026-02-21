# Layout and Hierarchy Reference

Visual hierarchy is the designer's equivalent of the copywriter's hook — if the eye doesn't land in the right place first, the message fails.

## The Five Levers of Visual Dominance

Every element's dominance is determined by its score across these five levers:

| Lever | How it works |
|-------|-------------|
| **Size** | Larger elements attract the eye first |
| **Weight** | Bold type and thick borders command attention |
| **Color** | High-contrast or saturated elements stand out from neutral fields |
| **Position** | Top-left and center capture attention before bottom-right |
| **Whitespace** | Isolated elements appear more important than clustered ones |

**The CTA rule:** The primary CTA should have the highest combined score across at least three of these five levers. If it doesn't, the hierarchy is broken.

## Scan Patterns

### F-Pattern (Web, Landing Pages)
The eye reads horizontally across the top, then moves down the left edge in smaller sweeps.

```
→→→→→→→→→→→→→→
→→→→→→→
→→→→
→
```

**Application:** Place the headline top-left, primary value proposition in the first horizontal sweep, CTA at the natural end of the first sweep or anchored left. Navigation belongs at the very top.

### Z-Pattern (Ads, Posters)
The eye moves top-left → top-right → bottom-left → bottom-right in a Z motion.

```
→→→→→→→→→→→→→
          ↙
→→→→→→→→→→→→→
```

**Application:** Hook top-left, brand/credibility signal top-right, supporting message bottom-left, CTA bottom-right. Works best for single-image ads with minimal text.

### Radial (Focal-Image Designs, Hero Posters)
The eye enters at the visual focal point (a face, a product, a bright element) and radiates outward.

**Application:** Place the primary message at or near the focal center. CTA in the lower third. Brand mark at the edge.

## Frame Dimensions by Design Type

| Design type | Frame size | Notes |
|-------------|------------|-------|
| Landing page | 1440×900px | Standard desktop. Design to 1280px content width max. |
| Meta / OG ad | 1200×628px | Facebook feed, link preview. Horizontal layout. |
| Square ad | 1080×1080px | Instagram, Meta square. Equal weight top/bottom. |
| Story ad | 1080×1920px | Full-bleed vertical. CTA in lower third. |
| LinkedIn ad | 1200×628px | Same as Meta but more formal treatment. |
| Poster (A4) | 2480×3508px at 300dpi | Print. Scale typography up significantly. |
| Poster (A3) | 3508×4961px at 300dpi | Large format. Even larger type. |

## Layout Templates by Design Type

### Landing Page (1440×900 viewport)

```
┌─────────────────────────────────┐
│ Navigation bar          72px   │
├─────────────────────────────────┤
│                                 │
│ HERO SECTION           600px   │
│ H1 → Subhead → CTA             │ ← Everything important is above this line
├─────────────────────────────────┤
│ Social proof strip      80px   │
├─────────────────────────────────┤
│ Features / Benefits    varies  │
├─────────────────────────────────┤
│ Secondary CTA          120px   │
├─────────────────────────────────┤
│ Footer                  80px   │
└─────────────────────────────────┘
```

**Rule:** The primary CTA must be visible without scrolling at 1440×900.

### Meta / OG Ad (1200×628)

```
┌─────────────────────────────────┐
│ [Brand mark]  Top-left 48×48px │
│                                 │
│      CENTRAL MESSAGE BLOCK      │
│      Headline + supporting      │
│                                 │
│  [CTA BUTTON]  Bottom third     │
└─────────────────────────────────┘
```

**Rule:** Background image or color fill. Headline must be legible against background. CTA button color must stand out from background and image.

### Square Ad (1080×1080)

```
┌────────────┐
│   HOOK     │ Upper third
│            │
│  [VISUAL]  │ Center (product, person, graphic)
│            │
│  [CTA BTN] │ Lower third — full width
└────────────┘
```

### Poster A4 at 300dpi (2480×3508)

```
┌─────────────────┐
│                 │
│   LARGE TITLE   │ Upper half — dominant
│                 │
│  [KEY VISUAL]   │ Center — image or graphic
│                 │
│  Details text   │ Lower third — smaller
│  Date / venue   │
│                 │
│  Brand / URL    │ Very bottom — smallest
└─────────────────┘
```

## The 8px Grid System

All margins, padding, and spacing must be multiples of 8:

| Use | Values |
|-----|--------|
| Component internal padding | 8, 16, 24px |
| Section margins | 32, 48, 64px |
| Section spacing (between sections) | 64, 96, 128px |
| Page edge margins | 48, 64, 80px |

For fine-grained work within components (icon alignment, label spacing), use the 4px sub-grid.

**Why 8px:** Most screen densities (1×, 1.5×, 2×) divide cleanly by 8, preventing sub-pixel rendering artifacts. It also constrains decisions — "48px or 56px?" becomes "48px or 64px?" which is a meaningful distinction.

## Common Hierarchy Mistakes

| Mistake | Fix |
|---------|-----|
| CTA has the same visual weight as the headline | Make the CTA button color more saturated and add whitespace around it |
| Decorative element (illustration, icon) is more dominant than the message | Reduce size, desaturate, or move the decoration to the background layer |
| Everything is bold | Reserve bold weight for H1 and the CTA label only |
| No clear entry point for the eye | Add a dominant hero element (large image, high-contrast headline) that anchors the scan |
| Section spacing is inconsistent | Audit against the 8px grid — pick two spacing values (e.g., 64px between sections, 32px within sections) and apply consistently |
