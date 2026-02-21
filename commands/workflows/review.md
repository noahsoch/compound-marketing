---
name: workflows:review
description: Perform comprehensive multi-specialist reviews of marketing assets using parallel review agents, with P1/P2/P3 severity findings
argument-hint: "[asset file path, campaign folder, or brief reference]"
---

# Review Marketing Assets

Perform exhaustive marketing reviews using multiple specialist agents in parallel.

## Introduction

This command takes a marketing asset (copy, email, ad, landing page, or brief) and runs it through relevant specialist review agents simultaneously. Findings are organized by severity and written to `docs/reviews/` for tracking.

## Review Target

<review_target> #$ARGUMENTS </review_target>

**If the target above is empty:**
Use **AskUserQuestion tool** to ask: "What would you like to review? Provide an asset file path, paste the copy directly, or describe what we're reviewing."

## Main Tasks

### 1. Determine Review Target and Load Assets

**Asset type detection:**
- File path provided → Read the file
- Copy pasted directly → Use as-is
- Brief reference → Read the brief and its associated assets
- No target → Ask

**Protected artifacts — never flag for deletion or removal:**
```
docs/ideations/*.md
docs/briefs/*.md
docs/insights/*.md
```

**Asset type classification:**
Once assets are loaded, classify what type of review applies:
- Written body copy → copy-reviewer (always)
- Email campaign (subject + body) → email-reviewer
- Landing page or sign-up flow → conversion-reviewer
- Web content (blog, article) → seo-reviewer
- Ad copy → ad-copy-reviewer
- Brand voice check → brand-voice-reviewer (if voice guide available)

### 2. Run Review Agents in Parallel

**Always run:**
- Task copy-reviewer(asset content)

**Run based on asset type (add to parallel batch):**
- Email → Task email-reviewer(email content)
- Landing page / sign-up → Task conversion-reviewer(page content)
- Blog / web content → Task seo-reviewer(content)
- Ads → Task ad-copy-reviewer(ad copy)
- Voice guide provided → Task brand-voice-reviewer(content + voice guide)

**Full campaign review (multiple assets in a campaign folder):**
Run the above agents plus these two cross-asset reviewers — always when reviewing a campaign folder:
- Task funnel-consistency-reviewer(all asset files + brief path) — checks promise→delivery chain, offer consistency across touchpoints, A/B variant differentiation
- Task metric-alignment-reviewer(all asset files + brief path) — checks every asset drives toward the brief's `target_metric`

Launch **all** applicable agents **simultaneously** in a single message.

**Single-asset vs campaign-folder review:**
- Single file path → run asset-type agents only
- Campaign folder path → run all agents including funnel-consistency-reviewer and metric-alignment-reviewer

### 3. Synthesize Findings

After all agents complete:

1. Collect all findings across agents
2. Remove duplicates (same issue flagged by multiple agents)
3. Assign severity:
   - **P1** — Blocks publish (legal risk, fundamental clarity failure, deliverability block)
   - **P2** — Should fix before publishing (conversion impact, voice deviation, structural problem)
   - **P3** — Nice-to-have improvement
4. Prioritize by impact

### 4. Write Finding Files

Detect output directory:
- Campaign folder detected → write to `{campaign}/reviews/`
- No campaign folder → write to `docs/reviews/`

```
{reviews_dir}/
├── 001-pending-p1-missing-cta.md
├── 002-pending-p2-funnel-broken-promise.md
├── 003-pending-p2-metric-cta-mismatch.md
└── 004-pending-p3-subject-line-length.md
```

**Naming convention:** `{number}-pending-{priority}-{description}.md`

**Each finding file contains:**
```markdown
---
status: pending
priority: p1|p2|p3
asset: [which asset this applies to]
agent: [which agent flagged it]
---

## [Finding Title]

**Issue:** [Specific description of the problem]
**Location:** [Where in the copy this appears]
**Impact:** [Why this matters]

**Proposed fix:**
> [Exact rewrite or specific action]

**Acceptance criteria:**
- [ ] [Testable criterion for when this is resolved]
```

Ensure `docs/reviews/` directory exists before writing.

### 5. Summary Report

After all finding files are created, present:

```markdown
## ✅ Review Complete

**Assets reviewed:** [list]
**Review agents used:** [list]
**Campaign funnel review:** [✓ included / — single asset, skipped]
**Metric alignment review:** [✓ included / — single asset, skipped]

### Findings Summary:
- **Total:** [X]
- **P1 — Blocks publish:** [count]
- **P2 — Should fix:** [count]
- **P3 — Nice-to-have:** [count]

### P1 Findings (Must Fix Before Publishing):
- `001-pending-p1-[description].md` — [brief description]

### P2 Findings (Should Fix):
- `002-pending-p2-[description].md` — [brief description]

### Funnel integrity: [Broken / Needs work / Solid]
### Metric readiness: [Blocked / Needs work / Ready]

### Next Steps:
1. Address P1 findings — required before publishing
2. Run `/workflows:create` to implement fixes
3. When P1s are clear, proceed to `/workflows:push`
4. Capture any learnings with `/workflows:report`
```

## What's Working vs. What Needs Work

Always include at least 2-3 specific things that are done well. Honest positive reinforcement helps writers understand the voice and approach to replicate.

## Important Guidelines

- **P1 issues block publish** — don't let assets go live with P1 findings open
- **Parallel agents** — always run applicable agents simultaneously, never sequentially
- **Protected artifacts** — never flag ideations, briefs, or insights for deletion
- **Specific fixes** — every finding needs a proposed rewrite or concrete action
