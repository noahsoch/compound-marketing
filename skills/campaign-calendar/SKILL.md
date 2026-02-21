---
name: campaign-calendar
description: Campaign timeline generation and milestone planning. Use after brief approval to create a backward-planned campaign calendar from event date to launch. Includes workflow command guidance for each milestone and optional Outlook calendar push via Microsoft Graph API.
---

# Campaign Calendar

Generate a backward-planned campaign calendar from an event or launch date. Each milestone includes the specific compound-marketing command to run and practical timing guidance.

## When to Use This Skill

Load this skill when:
- A campaign brief has been approved and has a specific event date or launch date
- A user needs a structured timeline with deadlines for campaign milestones
- A user wants to push milestones to their Outlook calendar

## T-Minus Timeline Framework

Work backward from the event/launch date:

| Milestone | T-minus | Command | Duration |
|-----------|---------|---------|---------|
| Ideation session | T−6 weeks | `/workflows:ideate` | ~30 min active |
| Brief + research | T−5 weeks | `/workflows:brief` | ~45 min active |
| Asset production | T−4 weeks | `/workflows:create` | 2-4 hours (swarm runs in background) |
| Full review | T−3.5 weeks | `/workflows:review` | ~1 hour active |
| Edits + approval | T−3 weeks | Manual | ~2 hours |
| Platform push | T−2.5 weeks | `/workflows:push` | ~30 min active |
| Ads go live | T−2 weeks | Platform activation | — |
| Email sequence begins | T−1.5 weeks | Email tool activation | — |
| A/B checkpoint | T−1 week | Manual — review data | ~30 min |
| Urgency phase / winner | T−3 days | Run winning variant | — |
| Event day | T−0 | Final push if applicable | — |
| Post-campaign report | T+3 days | `/workflows:report` | ~1 hour active |

## Calendar File Format

Write to `{campaign}/calendar/campaign-calendar.md`:

```markdown
---
campaign: {campaign-slug}
event_date: YYYY-MM-DD
created: YYYY-MM-DD
outlook_synced: false
milestones: [count]
---

# Campaign Calendar: {Campaign Name}

**Event date:** {date}
**Campaign slug:** {campaign-slug}

---

## Milestone Schedule

### 📅 [Date] — Ideation Session

**T-minus:** 6 weeks
**Command:** `/workflows:ideate "{campaign topic}"`

**What to do:** Explore campaign angles, hooks, and positioning. Have the brand-knowledge context loaded before this session.

**Tip:** The ideation agent works best when it can reference past campaign learnings from `docs/insights/`. Check if any relevant audience observations exist first.

**Time needed:** ~30 minutes of active interaction
**Output:** `campaigns/{slug}/plan/ideation.md`

---

### 📅 [Date] — Brief + Research

**T-minus:** 5 weeks
**Command:** `/workflows:brief campaigns/{slug}/plan/ideation.md`

**What to do:** Transform the ideation into a full brief. The command will automatically run event-context-researcher and icp-researcher in parallel.

**Tip:** Review the research outputs (event-research.md and icp-research.md) before approving the brief. Good research catches angles you would have missed.

**Time needed:** ~45 minutes (research runs in background, review takes active time)
**Output:** `campaigns/{slug}/plan/brief.md`

---

### 📅 [Date] — Asset Production

**T-minus:** 4 weeks
**Command:** `/workflows:create campaigns/{slug}/plan/brief.md`

**What to do:** Run the full swarm to produce all copy variants and Figma designs. This runs in the background — check back after the swarm completes.

**Tip:** Ensure your Figma MCP is authenticated before running. The swarm will produce all variants simultaneously.

**Time needed:** 2-4 hours (swarm runs in background; 30 min active for launch + review)
**Output:** All files in `campaigns/{slug}/assets/`

---

### 📅 [Date] — Full Review

**T-minus:** 3.5 weeks
**Command:** `/workflows:review campaigns/{slug}/`

**What to do:** Run all specialist reviewers + funnel consistency + metric alignment reviewers across every campaign asset simultaneously.

**Tip:** P1 findings block launch. Address them before edits/approval session.

**Time needed:** ~1 hour (reviewers run in parallel; time is reviewing findings)
**Output:** `campaigns/{slug}/reviews/`

---

### 📅 [Date] — Edits and Approval

**T-minus:** 3 weeks

**What to do:** Work through P1 and P2 findings from the review. Approve final variants. Make final decisions on which variants to push.

**Time needed:** ~2 hours
**Output:** Approved files marked in frontmatter as `status: approved`

---

### 📅 [Date] — Platform Push

**T-minus:** 2.5 weeks
**Command:** `/workflows:push campaigns/{slug}/`

**What to do:** Push approved assets to ad platforms, email tool, and Outlook calendar. Review all payloads before confirming.

**Tip:** Have platform credentials ready (Google Ads token, Meta access token, Mailchimp API key). The push command never auto-executes — you approve each platform push.

**Time needed:** ~30 minutes active
**Output:** `campaigns/{slug}/reports/push-log.md`

---

### 📅 [Date] — Ads Go Live

**T-minus:** 2 weeks

**What to do:** Confirm ad campaigns are active in each platform's dashboard. Verify tracking is firing correctly.

---

### 📅 [Date] — Email Sequence Begins

**T-minus:** 1.5 weeks

**What to do:** Confirm first email deployed. Monitor open rate in the first 24 hours.

---

### 📅 [Date] — A/B Checkpoint

**T-minus:** 1 week

**What to do:** Review A/B test data. Apply the decision framework from the `ab-testing` skill:
- >10% difference with >95% confidence → switch all traffic to winner
- <10% difference → keep running both, check secondary metrics
- Clear loser → pause losing variant, boost winner

**Reference:** `skills/ab-testing/SKILL.md` — "When to Call a Winner" section

---

### 📅 [Date] — Urgency Phase

**T-minus:** 3 days

**What to do:** Push final urgency emails and update ad creative to reflect deadline. Run winning ad variants only.

---

### 📅 [Date] — Event Day

**T-minus:** 0

**What to do:** Final engagement push if applicable. Monitor performance.

---

### 📅 [Date] — Post-Campaign Report

**T+3 days:** 3 days after event
**Command:** `/workflows:report "{campaign} — key learnings"`

**What to do:** Document what worked and what didn't while context is fresh. Capture winning copy, audience insights, and design preferences.

**Tip:** Run this within 48-72 hours while performance data is fresh and you remember why decisions were made.

**Time needed:** ~1 hour
**Output:** `campaigns/{slug}/reports/post-campaign-report.md` + entries in `docs/insights/`
```

## Outlook Calendar Push

After generating the calendar file, ask the user:
"Push these milestones to Outlook? This requires Microsoft Graph API setup. (Yes / Skip)"

If yes, refer to the push script in [scripts/push-to-outlook.js](./scripts/push-to-outlook.js).

Setup requirements:
1. Register an app in Azure Active Directory
2. Grant `Calendars.ReadWrite` permission
3. Get an OAuth token via device code flow or client credentials
4. Set `MICROSOFT_GRAPH_TOKEN` environment variable

The script reads the campaign calendar markdown, parses each milestone, and creates Outlook calendar events with:
- Title: `[Campaign] — {milestone name}`
- Start/end time: the milestone date, 9:00 AM local time, 1 hour duration
- Description: the full milestone instructions including the command to run
- Reminder: 24 hours before

## A/B Test Decision Timing

Reference section for the campaign calendar's A/B checkpoint:

**Minimum sample sizes:**
- Ads (Google/Meta): minimum 100 conversions per variant; run minimum 7 days
- Email subject lines: minimum 2 separate sends to different segments; or 1 send with 20%/20% split, remainder to winner
- Landing pages: minimum 200 visits per variant; minimum 7 days

**Decision framework (from `ab-testing` skill):**
- >10% improvement + >95% statistical confidence → declare winner, switch traffic
- <10% difference → run longer or call based on secondary metrics
- Bayesian approach for small audiences: >80% probability of being better is sufficient to act

**When to cut losses:**
- If a variant is performing >20% WORSE than the other after 7 days and 50+ conversions, pause it early — don't wait for full test duration
