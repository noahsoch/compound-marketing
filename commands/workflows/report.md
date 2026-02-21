---
name: workflows:report
description: Capture audience insights, winning copy, and campaign learnings to compound marketing knowledge in docs/insights/
argument-hint: "[optional: brief context about what was learned or what performed well]"
---

# /report

Coordinate multiple subagents working in parallel to document a marketing insight or campaign learning.

## Purpose

Captures what worked (and what didn't) while context is fresh, creating structured documentation in `docs/insights/` with YAML frontmatter for searchability and future reference. Uses parallel subagents for maximum efficiency.

**Why "report"?** Each documented insight compounds your marketing intelligence. The first time you figure out what language resonates with your audience takes research and guesswork. Document it, and the next campaign for that audience takes minutes. Knowledge compounds.

## Usage

```bash
/workflows:report                    # Document the most recent insight or result
/workflows:report [brief context]    # Provide additional context about what to capture
```

## Auto-Invoke Triggers

<auto_invoke>
<trigger_phrases>
- "that headline won"
- "the subject line crushed it"
- "great open rate"
- "interesting audience insight"
- "that copy worked"
- "strong results from"
- "the audience said"
- "it's converting well"
- "good performance"
</trigger_phrases>

<manual_override>
Use `/workflows:report [context]` to capture immediately without waiting for auto-detection.
</manual_override>
</auto_invoke>

## Execution Strategy: Two-Phase Orchestration

<critical_requirement>
**Only ONE file gets written — the final insight documentation.**

Phase 1 subagents return TEXT DATA to the orchestrator. They must NOT use Write, Edit, or create any files. Only the orchestrator (Phase 2) writes the final documentation file.
</critical_requirement>

### Phase 1: Parallel Research

<parallel_tasks>

Launch these subagents IN PARALLEL. Each returns text data to the orchestrator.

#### 1. Context Analyzer
- Extracts what asset or campaign is being documented
- Identifies what signal made this worth capturing
- Identifies the audience segment and channel
- Returns: YAML frontmatter skeleton with all fields classified against schema

#### 2. Insight Extractor
- Extracts the actual insight — the copy verbatim, or the audience observation in detail
- Identifies what specifically made this work (the mechanism, not just the result)
- Identifies specific metrics if available
- Returns: Core insight content block including actual copy and hypothesis

#### 3. Related Docs Finder
- Searches `docs/insights/` for related entries
- Identifies cross-references to add
- Returns: Paths and brief descriptions of related entries

#### 4. Pattern Identifier
- Determines if this is part of a larger pattern (3rd time this audience has responded to X)
- Identifies whether this entry should trigger a critical-patterns.md update
- Returns: Pattern analysis and promotion recommendation (yes/no + reasoning)

#### 5. Category Classifier
- Determines the correct `docs/insights/` subdirectory based on asset_type and performance_signal
- Validates category against schema
- Suggests filename following `[topic]-[audience]-[YYYYMMDD].md` convention
- Returns: Final path and filename

</parallel_tasks>

### Phase 2: Assembly and Write

<sequential_tasks>

**WAIT for all Phase 1 subagents to complete before proceeding.**

The orchestrating agent (main conversation) performs these steps:

1. Collect all text results from Phase 1 subagents
2. Assemble complete markdown file from collected pieces
3. Load `skills/swipe-file/schema.yaml` and validate YAML frontmatter against it
4. Create directory if needed: `mkdir -p docs/insights/[category]/`
5. Write the SINGLE final file: `docs/insights/[category]/[filename].md`

**If YAML validation fails:** Show specific errors, request corrections, then continue.

**File structure:**

```markdown
---
asset_type: [validated enum]
channel: [validated enum]
performance_signal: [validated enum]
audience_segment: "[specific description]"
campaign_type: [enum if applicable]
date: YYYY-MM-DD
tags: [tag1, tag2, tag3]
---

# [Descriptive Title]

## The Asset
[The actual copy verbatim, or the insight description]

## Context
[What campaign, what goal, what situation]

## Why It Worked (Hypothesis)
[The mechanism — why this resonated with this audience]

## The Result
[Specific metric or qualitative signal]

## How to Apply
[When to use this again — conditions, audience, channel]

## Related Entries
[Links to related docs/insights/ entries]
```

</sequential_tasks>

### Phase 3: Optional Enhancement

**WAIT for Phase 2 to complete before proceeding.**

<parallel_tasks>

Based on insight type, optionally invoke specialist agents to review the documentation:

- **Winning copy entry** → copy-reviewer validates the copy description is accurate and complete
- **Performance data** → performance-analyst adds interpretation of the metrics

</parallel_tasks>

## What It Captures

**Winning copy:**
- Exact copy verbatim (headlines, subject lines, CTAs, hooks that performed)
- What made them work — the mechanism and hypothesis
- When and how to apply again

**Audience insights:**
- Exact language the audience used to describe their problem (their words, not marketing speak)
- Objections they raise and what addresses them
- Desires they express and what resonates
- What they clicked on and why (behavioral signals)

**Failed approaches:**
- What didn't work and why (hypothesis)
- Conditions where it might still work
- What to try instead

## Preconditions

<preconditions enforcement="advisory">
  <check condition="signal_present">
    A meaningful performance signal exists (A/B test result, strong metric, observed audience behavior)
  </check>
  <check condition="non_trivial">
    The insight would save future you more than 10 minutes of guessing
  </check>
</preconditions>

## Category Directories

```
docs/insights/
├── winning-headlines/
├── winning-emails/
├── winning-ads/
├── winning-copy/
├── audience-observations/
├── campaign-learnings/
├── failed-approaches/
└── patterns/
    └── critical-patterns.md
```

## Common Mistakes to Avoid

| ❌ Wrong | ✅ Correct |
|----------|-----------|
| Subagents write draft files | Subagents return text; orchestrator writes one file |
| Research and assembly run in parallel | Research completes → then assembly runs |
| Insight captured without actual copy | Include the verbatim copy, not just a description |
| Audience segment is vague ("B2B") | Specific: "SaaS founders, 10-50 employees, bootstrapped" |

## Success Output

```
✓ Insight documented

Subagent Results:
  ✓ Context Analyzer: email_subject, email channel, winner_ab_test
  ✓ Insight Extractor: Subject line + open rate + mechanism
  ✓ Related Docs Finder: 1 related entry found
  ✓ Pattern Identifier: No pattern promotion needed (first instance)
  ✓ Category Classifier: winning-emails/

File created:
- docs/insights/winning-emails/urgency-subject-line-saas-founders-20260220.md

This insight is now searchable. When you next ideate or brief a campaign
for SaaS founders, the swipe-file-researcher will surface this automatically.

What's next?
1. Continue working (recommended)
2. Add to Required Reading — promote to critical-patterns.md
3. Link to related entries
4. View documentation
5. Other
```

## The Compounding Philosophy

```
Ideate → Brief → Create → Review → Publish → Report
   ↑                                              ↓
   └──────────────────────────────────────────────┘
```

First campaign for a new audience segment: research from scratch.
Document what you learn: `docs/insights/audience-observations/saas-founders-urgency.md`
Second campaign for the same audience: swipe-file-researcher surfaces the insight in seconds.

**Each marketing campaign should make subsequent campaigns easier — not harder.**
