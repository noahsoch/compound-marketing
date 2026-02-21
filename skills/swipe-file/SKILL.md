---
name: swipe-file
description: Captures winning copy, audience observations, and campaign insights as categorized documentation for future reference. Use after any campaign result, A/B test winner, or copy review to preserve institutional marketing knowledge.
disable-model-invocation: true
allowed-tools:
  - Read
  - Write
  - Bash
  - Grep
preconditions:
  - A marketing insight, result, or winning asset exists to document
---

# swipe-file Skill

**Purpose:** Automatically capture marketing insights — winning copy, audience observations, and campaign learnings — to build a searchable knowledge base that compounds over time.

## Overview

This skill captures marketing insights immediately after they're discovered, creating structured documentation in `docs/insights/` with YAML frontmatter for searchability. Each captured insight makes the next campaign faster and better.

**Organization:** Single-file architecture — each insight documented as one markdown file in its category directory (e.g., `docs/insights/winning-headlines/urgency-saas-founders-20260220.md`). Files use YAML frontmatter for metadata and searchability.

---

## 7-Step Process

### Step 1: Detect Capture Opportunity

**Auto-invoke after phrases:**
- "that headline won"
- "the subject line crushed it"
- "great open rate"
- "interesting audience insight"
- "that copy worked"
- "strong results from"
- "the audience said"

**OR manual:** `/workflows:report` command

**Non-trivial insights only:**
- A/B test winner with meaningful sample size
- Unexpected audience language or objection discovered
- Campaign that significantly outperformed baseline
- Counter-intuitive learning worth preserving

**Skip for:**
- Trivial one-time results with no generalizable insight
- Speculation without evidence
- Insights already documented

### Step 2: Gather Context

Extract from conversation or user input:

**Required information:**
- **Asset type**: What kind of content (headline, email subject, CTA, body copy, ad copy, etc.)
- **Channel**: Where it ran (email, Google Ads, Meta, LinkedIn, blog, etc.)
- **Performance signal**: What made it noteworthy (high CTR, A/B test winner, audience resonance, etc.)
- **Audience segment**: Who this insight applies to
- **The insight**: The actual content and/or what was learned

**BLOCKING REQUIREMENT:** If asset_type, channel, or the core insight is missing, ask and WAIT:

```
To document this properly, I need a few details:

1. What type of asset is this? (headline, email subject, CTA, body copy, ad copy, social post)
2. Which channel did this run on?
3. What specifically worked or was learned?

[Continue after user provides details]
```

### Step 3: Check Existing Docs

Search `docs/insights/` for similar entries:

```bash
# Search by similar audience or topic
grep -r "audience_segment" docs/insights/ | grep "[keyword]"
grep -r "tags:.*[keyword]" docs/insights/
```

**IF similar entry found:**

Present decision options:
```
Found similar insight: docs/insights/[path]

What's next?
1. Create new entry with cross-reference (recommended — different context)
2. Update existing entry (only if same exact insight with new data)
3. Other
```

WAIT for user response.

**ELSE:** Proceed directly to Step 4.

### Step 4: Generate Filename

Format: `[sanitized-topic]-[audience]-[YYYYMMDD].md`

**Sanitization rules:**
- Lowercase
- Replace spaces with hyphens
- Remove special characters except hyphens
- Keep reasonably short (< 60 chars)

**Examples:**
- `urgency-subject-line-saas-founders-20260220.md`
- `pain-point-hook-ecommerce-brands-20260315.md`
- `social-proof-cta-b2b-prospects-20260401.md`

### Step 5: Validate YAML Schema

**CRITICAL:** All entries require validated YAML frontmatter.

Load [yaml-schema.md](./references/yaml-schema.md) and classify the insight against the enum values. Ensure all required fields are present and match allowed values.

**BLOCK if validation fails:**
```
❌ YAML validation failed

Errors:
- asset_type: must be one of the schema enums, got "tagline"
- channel: must be one of the schema enums, got "twitter"
- performance_signal: required field missing

Please provide corrected values.
```

**Do NOT proceed to Step 6 until YAML passes all validation rules.**

### Step 6: Create Documentation

**Determine category from asset_type:** Use the category mapping in [yaml-schema.md](./references/yaml-schema.md).

**Create the insight file:**

```bash
CATEGORY="[mapped from asset_type or performance_signal]"
FILENAME="[generated-filename].md"
DOC_PATH="docs/insights/${CATEGORY}/${FILENAME}"

# Create directory if needed
mkdir -p "docs/insights/${CATEGORY}"
```

**Insight file structure:**

```markdown
---
asset_type: [enum value]
channel: [enum value]
performance_signal: [enum value]
audience_segment: "[specific segment description]"
campaign_type: [enum value]
date: YYYY-MM-DD
tags: [tag1, tag2, tag3]
---

# [Descriptive Title]

## The Asset
[The actual copy, headline, subject line, or description of the insight]

## Context
[Brief description of the campaign or situation — what was being sold, what the goal was]

## Why It Worked (Hypothesis)
[Best explanation of why this resonated — the mechanism, not just the result]

## The Result
[Specific metric if available, or qualitative signal — "47% open rate", "won A/B test", "audience used this exact phrase unprompted"]

## How to Apply
[When to use this pattern again — audience type, situation, channel]

## Related Entries
[Links to related docs/insights/ entries if any]
```

### Step 7: Cross-Reference & Pattern Detection

If similar entries found in Step 3, add cross-reference links to both files.

**Pattern promotion:** If this represents a pattern seen 3+ times across different campaigns:

```bash
# Add pattern summary to critical patterns file
cat >> docs/insights/patterns/critical-patterns.md << 'EOF'

## [Pattern Name]

**When it appears:** [Description]
**Why it works:** [Mechanism]
**Application:** [How to use it]

**Examples:**
- [Link to entry 1]
- [Link to entry 2]
- [Link to entry 3]
EOF
```

---

## Decision Menu After Capture

After documentation, present options:

```
✓ Insight captured

File created:
- docs/insights/[category]/[filename].md

What's next?
1. Continue working (recommended)
2. Add to Required Reading — promote to critical patterns
3. Link to related entries
4. View documentation
5. Other
```

---

## The Compounding Philosophy

```
Ideate → Brief → Create → Review → Report
   ↑                                    ↓
   └────────── (docs/insights/) ────────┘
```

First time writing for this audience segment: research from scratch (30 min).
Document the insight → `docs/insights/audience-observations/saas-founders-urgency.md`
Second campaign for this audience: swipe-file-researcher finds it in seconds.
Knowledge compounds.

---

## Quality Guidelines

**Good entries have:**
- ✅ The actual copy verbatim (not a summary)
- ✅ Specific audience segment (not "our customers")
- ✅ Specific performance signal (not "did well")
- ✅ A hypothesis for why it worked
- ✅ Guidance on when to apply again

**Avoid:**
- ❌ Vague descriptions ("good email")
- ❌ No actual copy or insight
- ❌ Missing audience context
- ❌ No application guidance

---

## Integration Points

**Invoked by:**
- `/workflows:report` command (primary interface)
- Manual invocation after strong campaign results
- Auto-detected after trigger phrases

**Feeds into:**
- `swipe-file-researcher` agent — searches this knowledge base before every campaign
- `/workflows:ideate` — surfaces relevant entries at campaign start
- `/workflows:brief` — incorporates proven patterns into briefs
