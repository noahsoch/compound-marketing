# Swipe File YAML Schema Reference

Every file in `docs/insights/` must include YAML frontmatter validated against this schema.

## Required Fields

### `asset_type`
What type of marketing asset this insight is about.

| Value | Description |
|-------|-------------|
| `headline` | Article, blog, or content headline |
| `body_copy` | Body copy for any format |
| `email_subject` | Email subject line |
| `preheader` | Email preheader text |
| `cta` | Call-to-action button or link text |
| `landing_page` | Full landing page or section |
| `ad_copy` | Paid ad copy (any platform) |
| `social_post` | Organic social media post |
| `case_study` | Customer success story |
| `video_script` | Video or reel script |
| `webinar_title` | Webinar or event title |

### `channel`
Where this asset ran or was published.

| Value | Description |
|-------|-------------|
| `email` | Email campaign or automation |
| `google_ads` | Google Search or Display |
| `meta_ads` | Facebook or Instagram ads |
| `linkedin_ads` | LinkedIn sponsored content |
| `seo_blog` | Search-optimized blog content |
| `newsletter` | Newsletter (owned or sponsored) |
| `social_organic` | Unpaid social media |
| `youtube` | YouTube video |
| `podcast` | Podcast episode or ad |
| `webinar` | Webinar or virtual event |
| `direct_outreach` | Cold email or DM |

### `performance_signal`
What made this worth capturing.

| Value | Description |
|-------|-------------|
| `high_ctr` | Click-through rate above benchmark |
| `high_conversion` | Conversion rate above benchmark |
| `high_open_rate` | Email open rate above benchmark |
| `high_reply_rate` | Reply or engagement rate above benchmark |
| `winner_ab_test` | Won a controlled A/B test |
| `audience_resonance` | Audience used this language unprompted or responded strongly |
| `viral_organic` | Spread significantly without paid promotion |
| `low_performance_note` | Documented as a failure — equally valuable |

### `audience_segment`
Free text. Be specific enough that a future researcher can tell if it applies to their campaign.

**Good:** `"SaaS founders, 10-50 employees, bootstrapped or early-stage funded"`
**Bad:** `"founders"` or `"B2B customers"`

### `date`
Date captured in `YYYY-MM-DD` format.

## Optional Fields

### `campaign_type`
The goal of the campaign this came from.

| Value | Description |
|-------|-------------|
| `awareness` | Building brand or category awareness |
| `acquisition` | Acquiring new customers or leads |
| `retention` | Keeping existing customers engaged |
| `reactivation` | Winning back churned or dormant users |
| `product_launch` | Launching a new product or feature |
| `content_marketing` | Organic content for SEO or authority |
| `event_promotion` | Webinar, conference, or event promotion |

### `tags`
Array of searchable keywords. Include:
- Emotional trigger: `urgency`, `curiosity`, `social_proof`, `fear_of_missing_out`, `aspiration`
- Format: `listicle`, `story`, `question`, `how_to`, `contrarian`
- Topic area: `pricing`, `onboarding`, `churn`, `feature_launch`

## Category Directory Mapping

The `asset_type` and `performance_signal` fields determine which `docs/insights/` subdirectory the file goes in:

| asset_type / signal | Directory |
|---------------------|-----------|
| `headline` | `docs/insights/winning-headlines/` |
| `email_subject`, `preheader` | `docs/insights/winning-emails/` |
| `ad_copy`, `social_post` | `docs/insights/winning-ads/` |
| `body_copy`, `cta`, `landing_page`, `case_study` | `docs/insights/winning-copy/` |
| `audience_resonance` signal | `docs/insights/audience-observations/` |
| `low_performance_note` signal | `docs/insights/failed-approaches/` |
| Everything else | `docs/insights/campaign-learnings/` |

## Complete Example

```yaml
---
asset_type: email_subject
channel: email
performance_signal: winner_ab_test
audience_segment: "SaaS founders, 10-50 employees, bootstrapped"
campaign_type: acquisition
date: 2026-02-20
tags: [urgency, curiosity, saas, subject-line]
---
```

---

## Design Asset Extensions (v1.2.0)

Design insights use the same YAML frontmatter schema with these additional enum values. All design insights are stored in `docs/insights/design-learnings/`.

### Additional `asset_type` values

| Value | Description |
|-------|-------------|
| `figma_frame` | A Figma frame — use when the insight applies to any design type |
| `landing_page_design` | Full landing page design generated or edited in Figma |
| `ad_design` | Static ad design in Figma (any platform) |
| `poster_design` | Print or digital poster design in Figma |

### Additional `performance_signal` values

| Value | Description |
|-------|-------------|
| `design_preference` | User edited a generated design — the edit reveals a reusable visual preference |
| `user_edited` | User made material changes that signal a systematic preference |

### Category directory for design insights

| asset_type or signal | Directory |
|---------------------|-----------|
| `figma_frame`, `landing_page_design`, `ad_design`, `poster_design` | `docs/insights/design-learnings/` |
| `design_preference`, `user_edited` | `docs/insights/design-learnings/` |

### Optional design-specific fields

These string fields are not enum-validated but should be included for all design insight entries:

| Field | Description |
|-------|-------------|
| `figma_url` | Live Figma URL of the design at time of capture |
| `original_spec_path` | Relative path to the generated spec in `docs/assets/` |

### Filename convention for design insights

`[topic]-[audience-keyword]-[YYYYMMDD].md`

Examples:
- `serif-hero-typography-dtc-ecommerce-20260221.md`
- `full-bleed-hero-image-saas-founders-20260221.md`
- `sticky-cta-header-enterprise-b2b-20260221.md`

### Complete design insight example

```yaml
---
asset_type: landing_page_design
channel: meta_ads
performance_signal: design_preference
audience_segment: "DTC e-commerce founders, Shopify stores, $500K–$5M ARR"
campaign_type: acquisition
date: 2026-02-21
tags: [design-learning, typography-preference, hero-treatment, premium-positioning, landing-page]
figma_url: "https://www.figma.com/file/abc123/..."
original_spec_path: "docs/assets/2026-02-21-design-landing-page-reactivation-spec.md"
---
```
