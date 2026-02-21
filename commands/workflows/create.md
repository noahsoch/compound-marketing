---
name: workflows:create
description: Produce marketing content and assets from a brief, executing all production tasks systematically with built-in copy review
argument-hint: "[brief file path or asset description]"
---

# Create Marketing Assets

Execute a marketing brief efficiently, producing complete, reviewed assets.

## Introduction

This command takes a brief document and produces the assets it specifies. The focus is on **shipping complete, reviewed assets** by understanding the brief thoroughly, drawing on proven copy patterns, and running quality checks before handoff.

## Input Document

<input_document> #$ARGUMENTS </input_document>

## Execution Workflow

### Phase 1: Quick Start

1. **Read Brief and Clarify**

   - Read the brief document completely
   - Review any reference links in the brief
   - If anything is unclear or ambiguous, ask clarifying questions now — once production starts, mid-stream questions waste time
   - Get clarity on: audience specifics, tone, word count, channel specs, CTA destination

2. **Swipe File Research**

   Before writing anything, run:

   - Task swipe-file-researcher("Find proven patterns for: [asset types in brief] for [audience segment]")

   Apply relevant insights — use proven audience language, tested headline structures, CTA patterns that have worked.

3. **Create Todo List**
   - Use TodoWrite to break the brief into individual asset tasks
   - One task per deliverable (each email, each headline set, each section)
   - Prioritize: foundational assets first (headlines → body → CTA → supporting)
   - Include copy review as a task after each major asset

### Phase 2: Execute

**Task Execution Loop:**

For each task in order:

```
while (tasks remain):
  - Mark task in_progress in TodoWrite
  - Load relevant framework from copywriting-frameworks skill
  - Check swipe file for relevant examples
  - Write asset following brief specs and framework
  - Mark task completed in TodoWrite
```

**Execution standards:**

- Follow the brief's core message strictly — don't drift
- Use the audience's language (from swipe file research or brief)
- Apply the appropriate framework (AIDA, PAS, FAB — see copywriting-frameworks skill)
- Write to the brief's specified tone
- Produce variations where the brief requests them (A/B headlines, subject lines)

**When writing copy:**

1. Start with the hook — the first line determines everything
2. Write the CTA before the body — know where you're taking them
3. Fill in the middle — connect hook to CTA with the core message
4. Cut ruthlessly — every sentence should earn its place

**For email specifically:**
- Write subject line options before body copy
- Preheader should complement subject, not repeat it
- Body should deliver on the subject line's promise within the first sentence

**For ads specifically:**
- Know the character limits before writing (Google: 30 chars headline, Meta: 125 chars primary text)
- Write 3+ headline variations — different hooks, different frameworks
- CTA button text matters: specific beats generic

### Phase 3: Quality Check

After completing each major asset (or all assets if brief is small), run copy review:

**Always run:**
- Task copy-reviewer("Review for clarity, persuasion, and engagement: [asset content]")

**Also run based on asset type:**
- Web content → Task seo-reviewer("Check SEO for: [content]")
- Email → Task email-reviewer("Review email for: [email content]")
- Ads → Task ad-copy-reviewer("Review ad copy for: [ad content]")

Run these **in parallel** when multiple reviewers apply.

**Address findings:**
- Fix all P1 findings before handoff (they block publish)
- Fix P2 findings unless there's a strong reason not to
- Document P3 findings as optional improvements

### Phase 4: Handoff

1. **Summary of deliverables**

   List all assets produced with file locations:
   ```
   ✓ Assets produced

   Email campaign:
   - docs/assets/2026-02-20-email-body.md
   - docs/assets/2026-02-20-subject-line-options.md

   Blog post:
   - docs/assets/2026-02-20-blog-saas-analytics.md

   All copy reviewed — [X] P1 issues fixed, [Y] P2 issues fixed
   ```

2. **Next steps via AskUserQuestion:**
   - Run `/workflows:review` for full multi-specialist review
   - Capture winning patterns with `/workflows:report`
   - Return to `/workflows:brief` if scope needs adjustment

---

## Output Location

All assets written to `docs/assets/YYYY-MM-DD-<type>-<descriptor>.<ext>`

Ensure `docs/assets/` directory exists before writing.

**No git workflow by default.** Assets are files handed off for human review and publishing. If working in a git-tracked website repo, follow your team's standard git workflow after handoff.

---

## Key Principles

### The Brief Is Your Source of Truth
- Don't drift from the brief's audience, message, or CTA
- If the brief is incomplete, ask now — not during production

### Frameworks Exist for a Reason
- Apply AIDA, PAS, or FAB from the copywriting-frameworks skill
- Decide which framework to use before writing, not after

### Prove Claims
- Never write a claim you can't back up with a fact, number, or customer quote
- If the brief doesn't provide proof points, ask

### Copy Review Before Handoff
- Don't skip the copy-reviewer — it catches problems you've gone blind to
- P1 issues are not negotiable

---

## Quality Checklist

Before handoff, verify:
- [ ] All brief tasks marked completed in TodoWrite
- [ ] Core message in brief is clear in every asset
- [ ] Every CTA is specific (action + destination)
- [ ] Copy reviewer run — all P1 issues fixed
- [ ] Subject lines and headlines have 3+ variations (for testing)
- [ ] Character limits respected (ads, subject lines)
- [ ] Assets written to docs/assets/ with clear filenames
