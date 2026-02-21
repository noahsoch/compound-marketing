---
name: workflows:push
description: Push approved campaign assets to external ad platforms and email tools. NEVER auto-executes — shows each payload and asks confirmation before any action. Requires approved assets in a campaign folder.
argument-hint: "[campaign folder path]"
---

# Push Campaign Assets to Platforms

The final step after `/workflows:review` has cleared P1 findings and assets are approved. Formats approved copy for each platform's API, shows the exact payload, and executes only after explicit confirmation.

**This command NEVER auto-executes.** Every platform push is shown as a payload for review before you confirm.

## Input

<input_target> #$ARGUMENTS </input_target>

**If empty:**
Use **AskUserQuestion tool** to ask: "Which campaign folder should we push? Provide the path (e.g. `campaigns/f1-weekend-2026`)."

---

## Phase 0: Pre-Push Verification

**1. Read the campaign folder structure:**

```
{campaign}/
├── plan/brief.md          ← read this for campaign context
├── assets/
│   ├── ads/               ← ad copy files for Google + Meta
│   └── emails/            ← email copy files for Mailchimp
├── reviews/               ← check P1 findings status
└── reports/               ← push-log.md will be written here
```

**2. Check P1 findings:**

Glob `{campaign}/reviews/*-pending-p1-*.md`. If any P1 findings exist with `status: pending`:

- List the open P1s
- Ask: "There are [N] open P1 findings. Pushing with open P1s can result in assets going live with critical issues. Proceed anyway, or fix P1s first?"
- If user chooses to fix first, stop here

**3. Verify approved assets exist:**

Glob for files with `status: approved` in frontmatter OR list all files in `{campaign}/assets/`. If no assets found, tell the user: "No assets found in `{campaign}/assets/`. Run `/workflows:create` first."

**4. Detect configured integrations:**

Check which environment variables are set. Announce which platforms are available:

```
Platform availability:
✓ Google Ads — GOOGLE_ADS_DEVELOPER_TOKEN set
✓ Meta Ads — META_ACCESS_TOKEN set
✗ Mailchimp — MAILCHIMP_API_KEY not set (skip or configure)
```

If no env vars are set, show setup instructions (see Platform Setup section at bottom) and exit.

---

## Phase 1: Platform Selection

Use **AskUserQuestion tool** to ask which platforms to push to (multi-select):

**Question:** "Which platforms should we push to?"

Show only the platforms with env vars configured. For each:
- Google Ads — format RSA campaigns from `{campaign}/assets/ads/`
- Meta Ads — format Meta ad sets from `{campaign}/assets/ads/`
- Mailchimp — draft email campaigns from `{campaign}/assets/emails/`
- Outlook Calendar — push campaign milestones from `{campaign}/calendar/campaign-calendar.md`

---

## Phase 2: Format and Review Per Platform

For each selected platform, run sequentially (not in parallel — each requires user confirmation before proceeding):

### Google Ads

1. Task `google-ads-formatter`(brief + ad copy files from `{campaign}/assets/ads/`). Agent returns formatted RSA payload JSON.

2. Show the payload:
```
── Google Ads Payload Preview ──────────────────────────────

Campaign: [campaign name]
Ad Group: [name]

Variant A:
  Headlines (15):
  1. [headline 1 — 30 chars max]
  2. [headline 2]
  ...
  Descriptions (4):
  1. [description 1 — 90 chars max]
  ...

Variant B:
  [same structure]

Final URL: [from brief]
──────────────────────────────────────────────────────────
```

3. Use **AskUserQuestion tool**: "Does this Google Ads payload look correct? Confirm to push, or cancel."
   - Confirm → POST to Google Ads API, log result
   - Edit → Ask what to change, regenerate
   - Cancel → Skip Google Ads, continue to next platform

### Meta Ads

1. Task `meta-ads-formatter`(brief + ad copy files). Agent returns structured Meta API payload.

2. Show the payload:
```
── Meta Ads Payload Preview ────────────────────────────────

Ad Set: [name]
Objective: [from brief target_metric]
Budget: [from brief if specified, else "not set"]

Ad A:
  Primary text: [≤125 chars]
  Headline: [≤40 chars]
  Description: [≤30 chars]
  Image: [placeholder — upload image separately in Meta Business]
  CTA: [button type]

Ad B:
  [same structure]
──────────────────────────────────────────────────────────
```

3. Use **AskUserQuestion tool**: "Does this Meta Ads payload look correct?"
   - Confirm → POST to Meta Marketing API, log result
   - Edit → Adjust and re-show
   - Cancel → Skip Meta

### Mailchimp

1. Task `mailchimp-formatter`(brief + email files from `{campaign}/assets/emails/`). Agent returns Mailchimp API payloads (one per email in the sequence).

2. Show the payload summary:
```
── Mailchimp Email Sequence Payload ─────────────────────────

Email 1 (A/B test):
  Subject A: [subject line A]
  Subject B: [subject line B]
  Test type: subject_line
  Split: 50/50 (send remainder to winner after 4 hours)
  List: [from brief or ask]

Email 2:
  Subject: [subject line]
  (no A/B — single variant)

[... etc]
──────────────────────────────────────────────────────────
```

3. Use **AskUserQuestion tool**: "Does this Mailchimp sequence look correct? These will be created as DRAFTS — you'll schedule sends separately in Mailchimp."
   - Confirm → POST to Mailchimp API (create drafts), log result
   - Edit → Adjust
   - Cancel → Skip Mailchimp

### Outlook Calendar

1. Read `{campaign}/calendar/campaign-calendar.md`. Check `outlook_synced` frontmatter.
   - If `outlook_synced: true` → ask: "Calendar was already synced. Re-sync (will create duplicate events) or skip?"

2. Show milestone summary:
```
── Outlook Calendar Payload ────────────────────────────────

[N] milestone events to create:
  • [date] — [Milestone 1 title]
  • [date] — [Milestone 2 title]
  ...

Calendar: [default / specified calendar-id]
Reminder: 24 hours before each event
──────────────────────────────────────────────────────────
```

3. Use **AskUserQuestion tool**: "Push these milestones to Outlook?"
   - Confirm → Run `node skills/campaign-calendar/scripts/push-to-outlook.js {campaign}/calendar/campaign-calendar.md`
   - Skip → Skip

---

## Phase 3: Write Push Log

After all platforms are processed, write `{campaign}/reports/push-log.md`:

```markdown
---
campaign: {campaign-slug}
pushed_date: YYYY-MM-DD
platforms: [google_ads, meta_ads, mailchimp, outlook]
---

# Push Log: {Campaign Name}

**Date:** YYYY-MM-DD

## Results

| Platform | Status | Notes |
|----------|--------|-------|
| Google Ads | ✓ Created / ✗ Failed / — Skipped | [error or ID if created] |
| Meta Ads | ✓ Created / ✗ Failed / — Skipped | [error or ID] |
| Mailchimp | ✓ Drafted / ✗ Failed / — Skipped | [campaign IDs] |
| Outlook Calendar | ✓ Synced / ✗ Failed / — Skipped | [event count] |

## Assets Pushed

[List of files pushed, with variant labels]

## Next Steps

- [ ] Schedule Mailchimp sends (in Mailchimp dashboard)
- [ ] Set Meta ad budgets and audience targeting (in Meta Business)
- [ ] Set Google Ads bids and budget (in Google Ads Manager)
- [ ] Monitor performance — A/B checkpoint: [date from calendar]
- [ ] Run `/workflows:report` after campaign completes
```

---

## Phase 4: Handoff

Present:

```
Push complete.

Platforms:
  Google Ads: [✓ / ✗ / —]
  Meta Ads: [✓ / ✗ / —]
  Mailchimp: [✓ / ✗ / —]
  Outlook: [✓ / ✗ / —]

Push log: {campaign}/reports/push-log.md

⚠ Manual steps required:
  - Schedule email sends in Mailchimp
  - Set budgets and targeting in ad platforms
  - Confirm tracking pixels are firing

Next: Run /workflows:report after the campaign ends to capture what worked.
```

---

## Platform Setup

### Google Ads

Required env vars:
```bash
export GOOGLE_ADS_DEVELOPER_TOKEN=your_token
export GOOGLE_ADS_CLIENT_ID=your_client_id
export GOOGLE_ADS_CLIENT_SECRET=your_secret
export GOOGLE_ADS_REFRESH_TOKEN=your_refresh_token
export GOOGLE_ADS_CUSTOMER_ID=your_customer_id  # e.g. 123-456-7890
```

Get credentials: Google Ads API → Apply for developer token → OAuth 2.0 setup.

### Meta Ads

Required env vars:
```bash
export META_ACCESS_TOKEN=your_page_access_token
export META_AD_ACCOUNT_ID=act_your_account_id  # e.g. act_123456789
```

Get credentials: Meta Business → Settings → System Users → Generate Token with `ads_management` permission.

### Mailchimp

Required env vars:
```bash
export MAILCHIMP_API_KEY=your_api_key-us1  # note: includes datacenter suffix
export MAILCHIMP_LIST_ID=your_audience_id
```

Get credentials: Mailchimp → Account → Extras → API Keys.

### Outlook

Required env var:
```bash
export MICROSOFT_GRAPH_TOKEN=your_oauth_token
```

Get credentials: Azure Active Directory → Register App → Calendars.ReadWrite permission → OAuth token via device code flow.

---

## Key Principles

- **Never auto-execute** — Every push requires explicit confirmation
- **Show before push** — Full payload must be visible before confirmation
- **Draft first** — Mailchimp creates drafts; email sends are always manual
- **Log everything** — Every push attempt (success or failure) is logged
- **Fail gracefully** — One platform failing does not stop others
