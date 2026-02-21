---
name: workflows:create
description: Produce marketing content and assets from a brief. Uses swarm-based parallel execution for complex multi-deliverable campaigns and sequential execution for simpler briefs. Generates A/B variants at creation time.
argument-hint: "[brief file path]"
---

# Create Marketing Assets

Execute a marketing brief to produce complete, reviewed assets. Automatically selects swarm mode (parallel) for complex campaigns or sequential mode for simple ones.

## Input Document

<input_document> #$ARGUMENTS </input_document>

**If the input above is empty:**
Use **AskUserQuestion tool** to ask: "Which brief should we produce assets from? Provide the path to a docs/briefs/ file."

---

## Phase 0: Load and Classify the Brief

1. **Read the brief completely.** Do not proceed until fully read.
2. **Detect campaign folder context:** Check if the brief lives under `campaigns/`. If yes, all outputs go to that campaign's `assets/` subfolder. If no, outputs go to `docs/assets/`.
3. **Clarify before producing:** If anything is ambiguous (audience specifics, tone, word count, channel specs, CTA destination), ask now via AskUserQuestion — one question at a time.
4. **Count deliverables** — extract the full asset list from the brief:
   - Ads (how many concepts × which platforms)
   - Emails (how many in the sequence)
   - Landing pages
   - Posters / print
   - Other copy assets
5. **Choose execution mode:**
   - **3 or more distinct deliverable types** → **Swarm Mode** (Section A)
   - **1–2 deliverable types** → **Sequential Mode** (Section B)

Announce the mode chosen: "Brief has [N] deliverable types — using [Swarm / Sequential] mode."

---

## Section A: Swarm Mode (3+ Deliverable Types)

### A1: Offer Foundation (Synchronous — Must Complete First)

Before spawning any parallel workers, the lead agent builds the shared offer foundation:

Read the brief and produce a **Offer & Hook Document** with:

```markdown
## Offer Foundation

### Core Offer
[What the campaign is selling — specific, factual, no fluff]

### Primary Value Proposition
[The single most important reason the audience should act — in their language]

### Proof Points
[Specific numbers, social proof, facts that support the offer]

### Hook Options (3-5)
[Different opening angles tested against different emotions: urgency, curiosity, FOMO, authority, contrarian]

### CTA
[Exact action + exact destination — no ambiguity]

### Tone and Voice
[3 adjectives that guide every word written: e.g., "Direct, urgent, insider"]

### Audience Language
[Specific phrases, pain points, desires from swipe file + brief — the words they use, not marketing-speak]

### Test Strategy
[From brief: single-variable | multi-concept]
```

Run swipe-file-researcher in parallel while building the foundation:
- Task swipe-file-researcher("Find proven patterns for: [asset types] for [audience segment]")

Write the Offer Foundation to `{output_dir}/offer-foundation.md`.

**Wait for the Offer Foundation to be complete before proceeding.**

### A2: Parallel Copy Production

Spawn copy workers **simultaneously** — each gets the full brief + Offer Foundation:

```
Run in parallel (Task with run_in_background: true):

Task ad-copywriter(
  brief + offer_foundation,
  output_dir,
  "Write [N] ad concepts × [test_strategy] variants for [platforms]"
)

Task email-copywriter(
  brief + offer_foundation,
  output_dir,
  "Write [N] email sequence with [test_strategy] variants"
)

Task lp-copywriter(
  brief + offer_foundation,
  output_dir,
  "Write landing page copy with A/B variant"
)

Task poster-copywriter(
  brief + offer_foundation,
  output_dir,
  "Write poster copy with A/B variant"
)
```

Each copy worker:
1. Reads the brief + Offer Foundation
2. Loads the `copywriting-frameworks` skill — selects the right framework for this asset type
3. Loads the `ab-testing` skill — generates variants per the test_strategy
4. Writes outputs to `{output_dir}/ads/`, `{output_dir}/emails/`, etc.
5. Runs its own copy review (Task copy-reviewer + asset-specific reviewer in parallel)
6. Reports completion with file paths

**Monitor all copy workers. Wait for ALL to complete before proceeding to A3.**

### A3: Parallel Figma Design (Blocked by Copy Completion)

Once all copy is complete, spawn Figma design workers **simultaneously**:

```
Run in parallel (Task with run_in_background: true):

Task figma-designer(
  brief + ad_copy_files,
  output_dir,
  "Design ad frames for all approved ad variants"
)

Task figma-designer(
  brief + email_copy_files,
  output_dir,
  "Design email layouts for all email variants"
)

Task figma-designer(
  brief + lp_copy_files,
  output_dir,
  "Design landing page frames for both LP variants"
)

Task figma-designer(
  brief + poster_copy_files,
  output_dir,
  "Design poster frames for both poster variants"
)
```

Each figma-designer instance:
1. Reads the brief and the specific copy files it's designing for
2. Follows the full `workflows:design` Phase 1-5 logic (design system discovery → planning → generation → review → spec)
3. Writes design specs to `{output_dir}/[asset-type]/[filename]-design-spec.md`

**Monitor all design workers. Wait for ALL to complete before proceeding to A4.**

### A4: Swarm Completion Summary

Collect all outputs from all workers. Present:

```
Campaign assets produced.

Copy files:
  Ads:
  - {output_dir}/ads/ad-copy-{concept}-a.md
  - {output_dir}/ads/ad-copy-{concept}-b.md
  ...

  Emails:
  - {output_dir}/emails/email-1-copy-a.md
  - {output_dir}/emails/email-1-copy-b.md
  ...

  Landing Page:
  - {output_dir}/landing-pages/lp-copy-a.md
  - {output_dir}/landing-pages/lp-copy-b.md

  Posters:
  - {output_dir}/posters/poster-copy-a.md
  - {output_dir}/posters/poster-copy-b.md

Design specs:
  [List all -design-spec.md files]

Review findings:
  P1 issues: [count — must fix before publishing]
  P2 issues: [count]
  P3 issues: [count]

Total deliverables: [N] copy files + [M] design specs
```

Use **AskUserQuestion** to offer next steps:
1. **Run full review** — `/workflows:review [campaign folder]` for multi-specialist review including funnel consistency
2. **View a specific asset** — Open any file to review
3. **Continue to calendar** — Check campaign timeline milestones
4. **Capture insights** — `/workflows:report` if there are learnings to document

---

## Section B: Sequential Mode (1–2 Deliverable Types)

### B1: Swipe File Research

Before writing:
- Task swipe-file-researcher("Find proven patterns for: [asset types] for [audience segment]")

Apply relevant insights — proven audience language, tested headline structures, CTA patterns.

### B2: Create Task List

Use TodoWrite to break the brief into individual asset tasks:
- One task per deliverable
- Load `ab-testing` skill to determine variants needed
- Include copy review as the final task

### B3: Execute Tasks

For each task:
1. Mark in_progress in TodoWrite
2. Load relevant framework from `copywriting-frameworks` skill
3. Check swipe file for relevant examples
4. Write the asset following brief specs and framework
5. Apply `ab-testing` skill — generate the B variant
6. Mark completed in TodoWrite

**When writing copy:**
- Start with the hook — the first line determines everything
- Write the CTA before the body — know where you're taking them
- Fill in the middle — connect hook to CTA with the core message
- Cut ruthlessly — every sentence should earn its place

**For email:** Subject line options before body. Preheader complements subject. Body delivers on subject line's promise within the first sentence.

**For ads:** Know character limits first. Write 3+ headline variations across different hooks.

### B4: Quality Review

After completing assets, run in parallel:
- Task copy-reviewer(all asset content)
- Task email-reviewer (if email content present)
- Task seo-reviewer (if web content present)
- Task ad-copy-reviewer (if ad copy present)

Address all P1 findings before handoff. Document P2 and P3.

### B5: Produce Figma Designs (if brief calls for it)

If the brief specifies visual design outputs, run `workflows:design` logic for each asset with a design requirement. Write design specs alongside the copy files.

### B6: Handoff Summary

```
Assets produced.

Files:
- {output_dir}/[filename-a].md (Variant A)
- {output_dir}/[filename-b].md (Variant B)
[+ design specs if applicable]

Review: [P1/P2/P3 counts]

Next: /workflows:review for full specialist review
```

---

## Output Location

**Campaign folder detected:** `campaigns/{name}/assets/{type}/`
**No campaign folder:** `docs/assets/YYYY-MM-DD-<type>-<descriptor>-<variant>.<ext>`

Variant naming: `{asset}-{concept}-{variant}.md`
Examples: `ad-copy-awareness-a.md`, `email-1-subject-b.md`, `lp-copy-challenger.md`

Ensure the output directory exists before writing.

---

## A/B Variant Standards

Load the `ab-testing` skill before generating any asset. Key rules:
- Every major asset gets a minimum of 2 variants (A = control, B = challenger)
- Each variant file includes in its frontmatter: `variant: A`, `test_variable: "headline"`, `hypothesis: "..."`
- Single-variable testing: change ONE thing between variants for clean data
- Multi-concept testing (when `test_strategy: multi-concept` in brief): vary the entire angle/approach

---

## Key Principles

- **The brief is your source of truth** — don't drift from audience, message, or CTA
- **Frameworks exist for a reason** — decide which framework to use before writing
- **Prove claims** — never write a claim without a fact, number, or quote from the brief
- **P1 issues are not negotiable** — fix before handoff
- **Variants by default** — every major asset needs a testable B variant
