---
name: campaign-folders
description: Campaign-scoped folder organization for multi-asset marketing campaigns. Use when starting a named campaign to scope all outputs under campaigns/{name}/ instead of the flat docs/ directories.
---

# Campaign Folders

Organizes all campaign outputs under a single named directory so campaigns don't bleed into each other. When a campaign folder is active, every command in the workflow writes to it automatically.

## When to Use This Skill

**Use campaign folders when:**
- Running a named, multi-asset campaign (ads + emails + landing pages + social)
- Managing multiple campaigns simultaneously (campaign A and B shouldn't share a folder)
- Working toward a specific event or launch date

**Skip campaign folders (use flat docs/ dirs) when:**
- Creating a one-off asset (single blog post, single email)
- Exploring ideas without committing to a campaign
- The ideation hasn't been named yet

## Folder Structure

```
campaigns/
  {campaign-slug}/
    plan/
      ideation.md          ← from /workflows:ideate
      brief.md             ← from /workflows:brief
      event-research.md    ← from event-context-researcher
      icp-research.md      ← from icp-researcher
    assets/
      ads/
        offer-foundation.md
        ad-copy-{concept}-a.md
        ad-copy-{concept}-b.md
        ad-design-{concept}-a-spec.md
      emails/
        email-1-copy-a.md
        email-1-copy-b.md
        email-1-design-spec.md
      landing-pages/
        lp-copy-a.md
        lp-copy-b.md
        lp-design-a-spec.md
        lp-design-b-spec.md
      posters/
        poster-copy-a.md
        poster-copy-b.md
        poster-design-spec.md
    reviews/
      review-final.md
      001-pending-p1-*.md
    calendar/
      campaign-calendar.md
    reports/
      post-campaign-report.md
      push-log.md
```

## Campaign Slug Convention

Convert the campaign name to a slug: lowercase, hyphens, date prefix optional.

| Campaign name | Slug |
|---------------|------|
| F1 Weekend Viewing Party | `f1-weekend-2026` |
| Q1 SaaS Reactivation | `q1-saas-reactivation` |
| April Product Launch | `2026-04-product-launch` |

## Creating a Campaign Folder

When a user says yes to starting a campaign folder, run:

```bash
mkdir -p campaigns/{slug}/plan
mkdir -p campaigns/{slug}/assets/ads
mkdir -p campaigns/{slug}/assets/emails
mkdir -p campaigns/{slug}/assets/landing-pages
mkdir -p campaigns/{slug}/assets/posters
mkdir -p campaigns/{slug}/reviews
mkdir -p campaigns/{slug}/calendar
mkdir -p campaigns/{slug}/reports
```

## Detecting Campaign Context

Any command can detect if it's operating within a campaign folder by checking the input file's path:

```
If the input file path starts with "campaigns/" → campaign is active
Extract campaign slug: second path segment after "campaigns/"
Set output_dir = "campaigns/{slug}/"
```

If no campaign context, fall back to:
- Ideations: `docs/ideations/`
- Briefs: `docs/briefs/`
- Assets: `docs/assets/`
- Reviews: `docs/reviews/`

## Brief Frontmatter for Campaign Context

When a brief is created within a campaign folder, include:

```yaml
---
campaign: {campaign-slug}
campaign_folder: campaigns/{campaign-slug}/
event_date: YYYY-MM-DD     # if applicable
test_strategy: single-variable | multi-concept
target_metric: email_subscribers | revenue | sign_ups | attendance
---
```

## Backward Compatibility

Campaign folders are opt-in. All existing flat `docs/` workflows continue to work unchanged. Only campaigns where the user explicitly names a campaign use the folder structure.
