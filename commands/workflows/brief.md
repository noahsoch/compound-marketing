---
name: workflows:brief
description: Transform a campaign idea or ideation document into a structured marketing brief ready for production
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

### 0. Idea Refinement

**Check for ideation output first:**

```bash
ls -la docs/ideations/*.md 2>/dev/null | head -10
```

**A recent ideation is relevant if:**
- The topic semantically matches the campaign description
- Created within the last 14 days
- If multiple candidates match, use the most recent one

**If a relevant ideation exists:**
1. Read the ideation document
2. Announce: "Found ideation from [date]: [topic]. Using as context for briefing."
3. Extract key decisions, chosen angle, audience, and open questions
4. **Skip idea refinement questions below** — the ideation already answered WHAT to do
5. Use ideation decisions as inputs to the research phase

**If no ideation found (or not relevant), run idea refinement:**

Use **AskUserQuestion tool** to refine through collaborative dialogue, one question at a time:
- Who specifically is this for?
- What one action should they take?
- What's the core message?
- What channel and format?

Continue until idea is clear OR user says "proceed."

### 1. Research (Always Runs — Parallel)

Run these agents **in parallel** to gather context:

- Task swipe-file-researcher(campaign_description)
- Task audience-researcher(campaign_description)

**What to look for:**
- **Swipe file:** proven copy patterns, audience language, what has worked for this segment and channel
- **Audience research:** ICP characteristics, objections, desired outcomes

These findings inform the brief's messaging and tone.

### 1.5 Research Decision

Based on campaign signals, decide whether external research is valuable:

**High-stakes campaigns → always add competitor research:**
- Product launches, competitive positioning, paid acquisition
- The cost of missing competitive context is too high

**Clear strategy → skip extra research:**
- Ideation doc already has strong strategic context
- User knows the audience well

**Announce the decision and proceed.**

### 1.5b Extended Research (Conditional)

**Only run if Step 1.5 indicates this is needed:**

- Task competitor-analyst(campaign_description)
- Task trend-spotter(campaign_description)

### 1.6 Consolidate Research

After research completes:
- Note audience language patterns from swipe file and audience research
- Identify proven angles for this segment/channel combination
- Note competitive context if applicable
- Capture any relevant swipe file entries

### 2. Brief Structure

**Title and categorization:**
- Draft a clear, searchable brief title
- Determine brief type: campaign, content, email, ad
- Convert to filename: date prefix + kebab-case + `-brief` suffix
  - Example: `2026-02-20-email-saas-reactivation-brief.md`

**Choose implementation detail level:**

#### 📄 MINIMAL
**Best for:** Single content pieces, simple ads, emails with clear direction

Includes: audience, core message, CTA, success metric

See template in [brief-templates skill](../../skills/brief-templates/SKILL.md)

#### 📋 STANDARD
**Best for:** Most campaigns, team collaboration

Includes everything in MINIMAL plus: objective, proof points, tone, channel specs, timeline

#### 📚 COMPREHENSIVE
**Best for:** Product launches, major campaigns, multi-channel efforts

Includes everything in STANDARD plus: competitive context, messaging hierarchy, asset list, measurement plan

### 3. Completeness Validation

After drafting the brief, run brief-writer agent to validate:

- Task brief-writer("Validate completeness of this brief and flag any gaps: [brief content]")

**Brief-writer checks:**
- Is the audience specific enough to write for?
- Is there a single core message (not multiple)?
- Is the CTA specific (action + destination)?
- Are success metrics measurable?

### 4. Issue Creation & Formatting

**Filename:** `docs/briefs/YYYY-MM-DD-<type>-<name>-brief.md`

Examples:
- `docs/briefs/2026-02-20-email-saas-reactivation-brief.md`
- `docs/briefs/2026-03-01-content-product-launch-blog-brief.md`
- `docs/briefs/2026-03-15-ad-linkedin-awareness-brief.md`

### 5. Final Review and Submission

**Pre-delivery checklist:**
- [ ] Audience is a specific person, not a demographic
- [ ] Core message is singular
- [ ] CTA specifies action AND destination
- [ ] Success metrics are measurable
- [ ] Proof points are factual (nothing we can't support)

## Output Format

Brief is written to: `docs/briefs/YYYY-MM-DD-<type>-<name>-brief.md`

Ensure `docs/briefs/` directory exists before writing.

## Post-Generation Options

After writing the brief, use **AskUserQuestion tool** to present options:

**Question:** "Brief ready at `docs/briefs/[filename]`. What would you like to do next?"

**Options:**
1. **Review and refine** - Improve the document through structured self-review (load `document-review` skill)
2. **Run `/workflows:review`** - Get specialist feedback on the brief's messaging strategy
3. **Start `/workflows:create`** - Begin producing content from this brief
4. **Open brief** - View the brief file

NEVER PRODUCE ASSETS! Just research and write the brief.
