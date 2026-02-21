---
name: ab-testing
description: A/B variant generation framework for marketing assets. Use when creating any major asset to produce testable variants with clear hypotheses. Documents what to vary, how to name variants, and when to call a winner.
---

# A/B Testing

Every major marketing asset should have a testable B variant. This skill defines what to vary, how to generate variants, how to name files, and when to call a winner.

## When to Use This Skill

Load this skill before producing any of these assets:
- Ad copy (any platform)
- Email copy (subject line + body)
- Landing page copy
- Poster / print copy

For short editorial pieces (blog posts, social posts not in a paid campaign), variants are optional.

## What to Vary by Asset Type

| Asset type | Variant A (Control) | Variant B (Challenger) | Output count |
|-----------|--------------------|-----------------------|--------------|
| Ad copy | Original hook/angle | Different hook or urgency frame | 2–3 per concept |
| Email subject line | Benefit-led | Curiosity/urgency frame | 2 per email |
| Email body | Standard CTA | Stronger urgency CTA | 2 per email |
| Landing page | Original headline + hero | Challenger headline or different value prop | 2 |
| Poster | Primary headline | Alternative angle/punchline | 2 |

## Test Strategy

Determined by `test_strategy` in the brief frontmatter.

### Single-Variable Testing (Default)

Change **one thing** between A and B. Everything else stays identical.

**Good single-variable tests:**
- Headline only (same body, same CTA)
- CTA button text only
- Opening hook only (same rest of email)
- Subject line only (same preview, same body)

**Why:** If you change two things and B wins, you don't know which change drove the result.

**When to use:** When you have enough traffic to reach significance on one variable, or when you have a specific hypothesis to test.

### Multi-Concept Testing

A and B are completely different approaches — different angle, different framework, different tone.

**Example:**
- A: PAS framework — lead with the problem
- B: FAB framework — lead with the feature and its benefit

**When to use:** Early in a campaign when you don't know which angle will resonate. Use to find the winning direction before optimizing individual variables.

## Generating Variants

### Step 1: State the Hypothesis

Before writing Variant B, write the hypothesis:

> "We believe [Variant B change] will outperform [Variant A baseline] for [audience segment] because [mechanism]. We'll know this worked if [specific metric] improves by [X%]."

Example:
> "We believe a curiosity-gap subject line ('You're leaving this on the table') will outperform the benefit-led subject line ('Get 47% more opens') for SaaS founders because this audience responds to insight framing over feature framing. We'll know this worked if open rate improves by >5%."

### Step 2: Write Variant A First

Complete the primary version fully. Don't think about B yet.

### Step 3: Write Variant B

Change only what the hypothesis specifies. Copy everything else from A.

If multi-concept: rewrite from scratch with a different framework/angle.

## Variant File Naming

```
{asset-type}-{concept}-{variant}.md
```

| Asset | Concept | Variant | Filename |
|-------|---------|---------|---------|
| Ad copy | awareness | A (control) | `ad-copy-awareness-a.md` |
| Ad copy | awareness | B (challenger) | `ad-copy-awareness-b.md` |
| Email 1 | reactivation | A | `email-1-copy-a.md` |
| Email 1 | reactivation | B | `email-1-copy-b.md` |
| Landing page | — | A | `lp-copy-a.md` |
| Landing page | — | B | `lp-copy-b.md` |
| Poster | event | A | `poster-copy-event-a.md` |
| Poster | event | B | `poster-copy-event-b.md` |

## Required Frontmatter for Every Variant File

```yaml
---
variant: A | B | C
test_variable: "subject line"
hypothesis: "Curiosity framing will outperform benefit framing for SaaS founders because..."
campaign: {campaign-slug}
asset_type: ad | email | landing_page | poster
concept: awareness | reactivation | launch | etc.
---
```

## Expected Output Counts

For a typical campaign (2 ad concepts, 3 emails, 1 LP, 1 poster):

| Asset | Concepts | Variants | Copy files | Design specs |
|-------|---------|---------|------------|-------------|
| Ads | 2 | 3 each | 6 | 6 |
| Emails | 3 | 2 each | 6 | 3 (design shared) |
| Landing page | 1 | 2 | 2 | 2 |
| Poster | 1 | 2 | 2 | 2 |
| **Total** | — | — | **16** | **13** |

## When to Call a Winner

### Sample Size Requirements

**Hard minimum:** 100 conversions per variant before calling a winner.

For smaller audiences, use Bayesian probability. A variant with 80% probability of being better is sufficient to act on when you can't reach 100 conversions.

### Recommended Test Duration

| Asset type | Minimum duration | Notes |
|-----------|-----------------|-------|
| Google/Meta ads | 7 days | Avoid day-of-week bias |
| Email subject line | 2 sends to different segments | Or same list, different time |
| Landing page | 7 days or 200 visits per variant | |
| Poster | N/A — pick winner before print run | |

### Decision Framework

```
If Variant B beats Variant A by >10% AND >95% confidence (or >80% Bayesian):
  → Switch all traffic to B
  → Capture the winning approach with /workflows:report

If difference is <10% OR sample is too small:
  → Keep running both
  → Check again at next A/B checkpoint (see campaign calendar)

If Variant A consistently leads:
  → Keep A running
  → Try a more different B in the next test cycle
```

### Secondary Metrics

When primary metrics are close, use secondary metrics to break the tie:
- Downstream conversion (not just CTR — did they actually buy/sign up?)
- Engagement depth (scroll depth, time on page for landing pages)
- Reply quality (for email — not just opens, but replies that indicate intent)
