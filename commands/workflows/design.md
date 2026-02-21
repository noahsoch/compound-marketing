---
name: workflows:design
description: Generate a Figma landing page, static ad, or poster design from a marketing brief. Reads existing design system tokens if a Figma URL is provided, checks captured design preferences, generates the design via Figma MCP, and stores a spec in docs/assets/ for future reflection and learning.
argument-hint: "[brief-file-path] [optional: figma-file-url]"
---

# Generate a Figma Design

Produce a Figma design from a marketing brief using the Figma MCP. The design spec is stored in `docs/assets/` so the user can edit freely in Figma and capture what they changed with `/workflows:design-reflect`.

## Design Target

<design_target> #$ARGUMENTS </design_target>

**If the target above is empty:**
Use **AskUserQuestion tool** to ask: "Which brief should this design be based on? Provide the path to a docs/briefs/ file."

## Main Tasks

### Phase 0: Resolve Inputs

Parse `$ARGUMENTS` to extract:
- **Brief path** (required): the first token ending in `.md`. If not provided, ask via AskUserQuestion.
- **Figma URL** (optional): any token starting with `https://www.figma.com/`. If absent, a new Figma file will be created (Path C).

Read the brief completely before proceeding. Identify the design type from the brief context: `landing_page`, `static_ad`, or `poster`. If unclear, default to `landing_page`.

### Phase 1: Design System Discovery

**Only run if a Figma URL was provided.** Run these MCP calls in parallel:

- `figma:get_variable_defs(url)` — extract color, spacing, and typography tokens
- `figma:get_metadata(url)` — extract layer names and existing structure
- `figma:get_design_context(url)` — extract layout and styling context

**Evaluate the result — choose a path:**

- **Path A** (design system found — tokens returned): Announce "Found design system with [N] colors, [M] text styles." Pass token data to figma-designer.
- **Path B** (Figma URL provided but no design system): Announce "No design system found. Will generate with brief-derived defaults, then bootstrap design system tokens." Pass null token data to figma-designer; set `design_system_bootstrapped: true` in the spec.
- **Path C** (no Figma URL): Skip Phase 1. figma-designer will generate to a new file. The returned URL becomes the canonical Figma reference.

### Phase 2: Design Planning

Task `figma-designer` with:
- Brief content (full text)
- Design system data (from Phase 1, or null for Path C)
- Figma URL (or null for Path C)
- Design type (inferred in Phase 0)

Wait for `figma-designer` to return the complete design spec. If the spec contains P1 pre-generation flags, resolve them before proceeding to Phase 3 (adjust the plan, not skip generation).

### Phase 3: Generate in Figma

The orchestrator calls:
```
figma:generate_figma_design(description)
```

Where `description` is the structured design spec from Phase 2, translated into natural language precise enough for the tool to produce a close approximation — frame size, section heights, exact copy, color values, typeface names, font sizes, and hierarchy intent.

Store the returned:
- `figma_url` — the live Figma file URL
- `figma_frame_id` — the specific frame ID

**Path B only:** After generation, call `figma:create_design_system_rules(figma_url)` to bootstrap a design system stub for future consistency.

### Phase 4: Design Review

Run in parallel:

- Task `figma-design-reviewer` with the design spec, brief content, and Figma frame URL
- `figma:get_screenshot(figma_url)` — capture a visual snapshot for context

Wait for both to complete. The screenshot is context; the reviewer findings are the deliverable.

### Phase 5: Write Design Spec

Ensure `docs/assets/` directory exists. Write the design spec to:

```
docs/assets/YYYY-MM-DD-design-<type>-<descriptor>-spec.md
```

Where `<type>` = `landing-page`, `static-ad`, or `poster`; `<descriptor>` = 2-3 kebab-case words from the brief topic.

**Spec format:**

```markdown
---
design_type: landing_page | static_ad | poster
brief_path: docs/briefs/[filename].md
figma_url: https://www.figma.com/file/...
figma_frame_id: [frame ID]
generated_date: YYYY-MM-DD
design_system_bootstrapped: true | false
---

# Design Spec: [Title from Brief]

## Design Intent
[The core visual message and emotional tone — what this design should make the viewer feel and do]

## Layout Structure
[Frame dimensions, sections, grid system, hierarchy decisions]

## Typography
| Element | Family | Weight | Size | Line-height | Color |
|---------|--------|--------|------|-------------|-------|
| H1 | | | | | |
| H2 | | | | | |
| Body | | | | | |
| CTA label | | | | | |

## Color Palette
| Role | Value | Usage |
|------|-------|-------|
| Primary | | CTA button |
| Secondary | | Section backgrounds |
| Neutral dark | | Headings |
| Neutral mid | | Body text |
| Neutral light | | Page background |

## Layer Inventory
[Named layers as generated — list every major layer with its Figma layer name]

## Copy Blocks
[All text content placed in the design, verbatim, keyed to layer name]

## Review Findings
### P1 — Blocks Publish
[Findings from figma-design-reviewer, or "None"]

### P2 — Should Fix
[Findings, or "None"]

### P3 — Nice-to-Have
[Findings, or "None"]

## Figma URL
[Live link to open in Figma]

## Next Steps
- Open in Figma and review the design visually
- Edit anything that doesn't match your vision
- When done editing, run `/workflows:design-reflect [figma_url] docs/assets/[this-spec-filename].md` to capture what you changed as design preferences
- Or run `/workflows:report` if there are copy insights from the brief to capture
```

### Phase 6: Handoff

Display the summary:

```
Design generated.

Figma frame: [URL — open in Figma]
Spec stored: docs/assets/[filename]-spec.md

Review summary:
- P1 (blocks publish): [count] — [brief descriptions, or "None"]
- P2 (should fix): [count]
- P3 (nice-to-have): [count]
```

Use **AskUserQuestion tool** to present next steps:

**Question:** "Design ready. What would you like to do next?"

**Options:**
1. **Open in Figma** — Open the Figma frame directly
2. **Fix P1/P2 findings** — Address review findings before editing
3. **Run design-reflect after editing** — Edit in Figma, then run `/workflows:design-reflect` to capture preferences
4. **Continue with other work** — Return to the copy or campaign workflow

## Protected Artifacts

Never flag for deletion or removal:
```
docs/ideations/*.md
docs/briefs/*.md
docs/insights/*.md
```

## Important Guidelines

- **Spec is the source of truth** — everything in the spec must match what was actually generated in Figma. If generation produces something different from the plan, update the spec to reflect reality before writing it.
- **Figma URL is required** — the spec cannot be useful for `workflows:design-reflect` without a valid Figma URL. If the MCP returns no URL, surface this as an error before writing the spec.
- **One spec file per generation** — do not write intermediate draft specs. Write the final spec once, after review is complete.
- **Design system tokens** — if Path A (existing design system), reference token names in the spec's color and typography sections, not raw hex values.
