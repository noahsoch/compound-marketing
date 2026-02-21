---
name: workflows:design-reflect
description: Capture design preference insights after manually editing a Figma design. Reads the current Figma state via MCP, compares it to the original generated spec, asks the user why they made changes, and documents the preferences in docs/insights/design-learnings/ to compound into future design runs.
argument-hint: "[figma-url] [optional: spec-file-path]"
---

# Capture Design Preferences from Figma Edits

Document what you changed in Figma and why — turning your edits into compounding design intelligence that improves future `/workflows:design` runs automatically.

## Purpose

The Figma MCP has no change detection. This command bridges the gap: it reads the **current** Figma state, compares it to the **original generated spec** stored in `docs/assets/`, and asks targeted questions to extract the reasoning behind your edits. That reasoning becomes a reusable preference signal stored in `docs/insights/design-learnings/`.

## Reflect Target

<reflect_target> #$ARGUMENTS </reflect_target>

**If the target above is empty:**
Use **AskUserQuestion tool** to ask: "What's the Figma URL of the design you edited? Also, do you know the path to the original spec file in docs/assets/?"

## Main Tasks

### Phase 0: Resolve Inputs

Parse `$ARGUMENTS`:
- **Figma URL** (required): any token starting with `https://www.figma.com/`. If not provided, ask.
- **Spec path** (optional): a path ending in `-spec.md`. If not provided, auto-detect.

**Auto-detect spec:** If no spec path provided, find the most recently modified design spec:

```bash
ls -t docs/assets/*-design-*-spec.md 2>/dev/null | head -5
```

Present the top result: "Auto-detected spec: `docs/assets/[filename]`. Is this the right spec for the design you edited?"

- If yes: proceed.
- If no: ask for the correct path.

Read the spec file completely before proceeding.

### Phase 1: Current State Capture

Run in parallel — wait for all to complete before Phase 2:

- `figma:get_design_context(figma_url)` — current layout and styling context
- `figma:get_metadata(figma_url)` — current layer names, positions, sizes, types
- `figma:get_screenshot(figma_url)` — visual snapshot for context

The current state data (design_context + metadata) combined with the original spec is the input to `design-reflector`.

### Phase 2: Reflection

Task `design-reflector` with:
- Original spec content (full text)
- Current design_context result
- Current metadata result
- Current screenshot
- figma_url

`design-reflector` will:
1. Identify structural deltas between the spec and current state
2. Ask the user targeted questions (one at a time, max 3) via AskUserQuestion
3. Synthesize answers into preference signals
4. Return structured preference data

**Wait for `design-reflector` to complete.** The agent's dialogue with the user must complete before Phase 3 begins — the preference signals are the input to Phase 3.

### Phase 3: Parallel Research

**CRITICAL: Phase 3 subagents return TEXT DATA only. They must NOT write any files.**

Launch these in parallel — each returns text to the orchestrator:

#### Context Analyzer
- Classifies the insight against the updated swipe-file schema
- Determines `asset_type` (figma_frame | landing_page_design | ad_design | poster_design based on the spec's design_type)
- Confirms `performance_signal` = `design_preference`
- Extracts `audience_segment`, `channel`, `campaign_type` from the spec
- Returns: completed YAML frontmatter skeleton

#### Category Classifier
- Confirms output directory: `docs/insights/design-learnings/`
- Suggests filename following `[topic]-[audience-keyword]-[YYYYMMDD].md` convention
- Example: `serif-hero-typography-dtc-ecommerce-20260221.md`
- Returns: full file path

#### Related Docs Finder
- Searches `docs/insights/design-learnings/` for related entries:
  ```
  Grep: pattern="audience_segment:.*[audience keyword]" path=docs/insights/design-learnings/ -i=true
  Grep: pattern="tags:.*[design-type keyword]" path=docs/insights/design-learnings/ -i=true
  ```
- Returns: paths and brief descriptions of related entries to cross-reference

**Wait for all three to complete.**

### Phase 4: Assemble and Write

The orchestrator (not the subagents) performs these steps:

1. Collect all text results from Phase 3
2. Validate YAML frontmatter against `skills/swipe-file/schema.yaml` (the updated schema includes design enums)
3. Create directory if needed: `mkdir -p docs/insights/design-learnings/`
4. Assemble the complete insight file
5. Write the **single** final file: `docs/insights/design-learnings/[filename].md`

**If YAML validation fails:** Show specific errors, apply corrections, then write.

**Insight file format:**

```markdown
---
asset_type: [figma_frame | landing_page_design | ad_design | poster_design]
channel: [from spec/brief — e.g., meta_ads, google_ads, email]
performance_signal: design_preference
audience_segment: "[from spec — specific description]"
campaign_type: [from spec, if present]
date: YYYY-MM-DD
tags: [design-learning, typography-preference | layout-preference | color-preference, hero-treatment, etc.]
figma_url: "[current figma url]"
original_spec_path: "[relative path to spec file]"
---

# Design Preference: [Descriptive Title from design-reflector]

## What Was Changed
[The specific edits made — from the Changes Detected table in design-reflector's output]

## The Original (Generated)
[What the generation produced — values from the original spec]

## The Edit (User's Version)
[What the user changed it to — specific values: "changed hero font from Inter 700 50px to Playfair Display 700 72px"]

## Why (Captured from User)
[The user's explanation, verbatim or close paraphrase]

## The Preference Signal
[The synthesized reusable rule from design-reflector — formatted as "For [design type] targeting [audience], when [condition], prefer [X] over [Y] because [mechanism]"]

## How to Apply
[When to default to this preference: design type, audience, context conditions]

## Related Entries
[Links to related docs/insights/design-learnings/ entries from Related Docs Finder]
```

### Phase 5: Handoff

Display the summary:

```
Design preference captured.

File: docs/insights/design-learnings/[filename].md

Changes documented:
- [Bullet list of change categories: e.g., "Typography: hero font changed to serif display"]
- [...]

Preference signal:
"[The reusable rule — one sentence]"

This preference will inform future /workflows:design runs for [audience segment].

Next:
1. Continue refining in Figma — run /workflows:design-reflect again after more edits
2. Run /workflows:report if there are copy insights from this campaign to capture
3. Return to the workflow — run /workflows:review on the final asset when ready
```

## Important Guidelines

- **Only ONE file gets written** — the final insight doc in `docs/insights/design-learnings/`. Phase 3 subagents return TEXT only.
- **Phase 2 must complete before Phase 3** — the preference signals from `design-reflector` are the content that Phase 3 classifies and Phase 4 writes.
- **Copy changes are separate** — if `design-reflector` flags text content changes, note them in the handoff as candidates for `/workflows:report`, not captured here.
- **The spec path matters** — if the wrong spec is used, the delta comparison will be meaningless. Always confirm the spec with the user if auto-detected.
- **This command gets more valuable over time** — each reflection cycle adds to `docs/insights/design-learnings/`, which `figma-designer` reads automatically before the next design run. The more entries, the fewer manual corrections the user needs to make.

## Common Mistakes to Avoid

| ❌ Wrong | ✅ Correct |
|---------|----------|
| Subagents write draft files in Phase 3 | Subagents return text; orchestrator writes one file in Phase 4 |
| Phase 3 runs before Phase 2 completes | Phase 2 (design-reflector dialogue) must complete first |
| Trivial pixel nudges treated as preferences | Ignore changes < 4px unless systematic across 3+ elements |
| User explanation "I liked it better" is synthesized into a rule | Probe once for specificity; if still vague, record as aesthetic preference without a reusable rule |
| Copy changes captured here | Flag for /workflows:report instead |
