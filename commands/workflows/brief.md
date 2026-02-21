---
name: workflows:brief
description: Transform a campaign idea or ideation document into a structured marketing brief ready for production. Runs event research and ICP research in parallel. Automatically generates a campaign calendar after brief approval if an event date is specified.
argument-hint: "[campaign idea, ideation doc path, or brief description]"
---

# Create a Marketing Brief

## Introduction

**Note: The current year is 2026.** Use this when dating briefs.

Transform campaign ideas, ideation documents, or rough descriptions into well-structured brief files that follow best practices. Provides flexible detail levels to match campaign complexity.

## Campaign Description

<campaign_description> #$ARGUMENTS </campaign_description>

**If the description above is empty, ask the user:** "What would you like to brief? Describe the campaign, content piece, or asset you want to create."

Do not proceed until you have a clear campaign description from the user.

## Main Tasks

### 0. Idea Refinement and Campaign Folder Detection

**Check for ideation output first:**

If a file path is provided, read it. Then check if it lives under `campaigns/`:

```bash
# Check for recent ideations in docs/
ls -la docs/ideations/*.md 2>/dev/null | head -10
# Check for campaign-scoped ideation
ls -la campaigns/*/plan/ideation.md 2>/dev/null | head -10
```

**A relevant ideation exists if:**
- The topic semantically matches the campaign description
- Created within the last 14 days

**If a relevant ideation exists:**
1. Read the ideation document
2. Announce: "Found ideation from [date]: [topic]. Using as context for briefing."
3. Extract: chosen angle, audience, channel, open questions, campaign slug (if set)
4. **Skip idea refinement questions below** — the ideation already answered WHAT to do
5. Use ideation decisions as inputs to the research phase

**Detect campaign folder context:**

If the ideation has a `campaign:` frontmatter field OR the file path starts with `campaigns/`:
- Set `campaign_slug` and `campaign_folder = campaigns/{slug}/`
- Brief output goes to `{campaign_folder}/plan/brief.md`
- Announce: "Campaign folder detected: `{campaign_folder}`. Brief and all outputs will be scoped to this campaign."

If no campaign context:
- Brief output goes to `docs/briefs/YYYY-MM-DD-<type>-<name>-brief.md`

**If no ideation found, run idea refinement:**

Use **AskUserQuestion tool** one question at a time:
- Who specifically is this for?
- What one action should they take?
- What's the core message?
- What channel and format?
- Is this for a specific event or launch date? (if yes, capture the date — needed for calendar)

Also ask: "Is this part of a named campaign? If yes, provide a slug (e.g. `summer-launch-2026`)."
If slug provided, create campaign folder structure and set `campaign_folder`.

Continue until idea is clear OR user says "proceed."

### 1. Research (Always Runs — Parallel)

Run these agents **simultaneously** in a single message:

- Task swipe-file-researcher(campaign_description)

**If campaign has a specific event or the audience research would benefit from external context:**
- Task event-context-researcher(campaign_description + event details if known)
- Task icp-researcher(campaign_description + audience signals from ideation)

**What each returns:**
- **swipe-file-researcher:** Proven copy patterns, audience language, what has worked for this segment and channel. Grep-first from `docs/insights/`.
- **event-context-researcher:** Event-specific hooks, timing windows, cultural moments, competitor campaign landscape. Saves to `{campaign_folder}/plan/event-research.md` if campaign folder exists.
- **icp-researcher:** Audience demographics, community language (verbatim quotes), pain points, where they spend time. Saves to `{campaign_folder}/plan/icp-research.md` if campaign folder exists.

Wait for all research agents to complete before proceeding.

### 1.5 Research Consolidation

Consolidate research findings:
- Audience language patterns (verbatim phrases from ICP research — use these in copy, not marketing-speak)
- Event-specific angles and timing windows (from event research)
- Proven copy patterns and structural approaches (from swipe file)
- Competitive gaps or whitespace (from event research)
- Key audience objections and desires (from ICP research)

Note: if research agents produced files to `{campaign_folder}/plan/`, reference them in the brief frontmatter.

### 2. Brief Structure

**Title and categorization:**
- Draft a clear, searchable brief title
- Determine brief type: campaign, content, email, ad
- In campaign folder: save as `{campaign_folder}/plan/brief.md`
- Without campaign folder: `docs/briefs/YYYY-MM-DD-<type>-<name>-brief.md`

**Brief frontmatter (always include these fields for downstream commands):**

```yaml
---
campaign: {slug or omit}
campaign_folder: campaigns/{slug}/ (or omit)
event_date: YYYY-MM-DD (if known)
target_metric: email_subscribers | revenue | sign_ups | attendance | leads | app_installs
test_strategy: single-variable | multi-concept
brief_type: campaign | content | email | ad
research_event: {campaign_folder}/plan/event-research.md (if exists)
research_icp: {campaign_folder}/plan/icp-research.md (if exists)
---
```

**Choose implementation detail level:**

#### 📄 MINIMAL
**Best for:** Single content pieces, simple ads, emails with clear direction

Includes: audience, core message, CTA, success metric

#### 📋 STANDARD
**Best for:** Most campaigns, team collaboration

Includes everything in MINIMAL plus: objective, proof points, tone, channel specs, asset list, timeline

#### 📚 COMPREHENSIVE
**Best for:** Product launches, major campaigns, multi-channel efforts

Includes everything in STANDARD plus: competitive context, messaging hierarchy, full asset list with variants, measurement plan

See templates in [brief-templates skill](../../skills/brief-templates/SKILL.md)

### 3. Completeness Validation

After drafting the brief, run brief-writer agent to validate:

- Task brief-writer("Validate completeness of this brief and flag any gaps: [brief content]")

**Brief-writer checks:**
- Is the audience specific enough to write for?
- Is there a single core message (not multiple)?
- Is the CTA specific (action + destination)?
- Are success metrics measurable?
- Is `target_metric` set in frontmatter? (required for metric-alignment-reviewer later)
- Is `test_strategy` set? (required for ab-testing skill in /workflows:create)

### 4. Write Brief File

Write to the appropriate path. Ensure directory exists before writing.

**Pre-delivery checklist:**
- [ ] Audience is a specific person, not a demographic
- [ ] Core message is singular
- [ ] CTA specifies action AND destination (`cta_destination` in frontmatter)
- [ ] Success metrics are measurable
- [ ] Proof points are factual (nothing we can't support)
- [ ] `target_metric` in frontmatter
- [ ] `test_strategy` in frontmatter

### 5. Post-Brief: Campaign Calendar (If Event Date Present)

After the brief is written and validated, check if `event_date` is set in the brief frontmatter.

**If event_date is present:**

Load the `campaign-calendar` skill. Generate a backward-planned campaign calendar:

1. Announce: "Event date detected: [date]. Generating campaign calendar..."
2. Apply the T-minus timeline framework from the `campaign-calendar` skill
3. Calculate all milestone dates working backward from the event date
4. Write the calendar to `{campaign_folder}/calendar/campaign-calendar.md` (or `docs/calendar/YYYY-MM-DD-{slug}-calendar.md` if no campaign folder)

Then ask: "Campaign calendar generated. Push these milestones to Outlook? This requires a Microsoft Graph API token (`MICROSOFT_GRAPH_TOKEN` env var). (Yes / Skip)"

If yes: instruct user to run:
```bash
node skills/campaign-calendar/scripts/push-to-outlook.js {campaign_folder}/calendar/campaign-calendar.md
```

**If no event_date:** Skip calendar generation.

### 6. Post-Generation Options

After writing the brief (and calendar if applicable), use **AskUserQuestion tool**:

**Question:** "Brief ready. What would you like to do next?"

**Options:**
1. **Review and refine** - Load `document-review` skill to improve the brief
2. **Start `/workflows:create`** - Begin producing assets from this brief
3. **Run `/workflows:review`** - Get specialist feedback on the brief's messaging strategy
4. **Open brief** - View the brief file

NEVER PRODUCE ASSETS! Just research, write the brief, and generate the calendar.
