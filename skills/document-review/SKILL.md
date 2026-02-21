---
name: document-review
description: Refines ideation or brief documents before proceeding to the next workflow step. Use when an ideation or brief document exists and needs improvement before briefing or production begins.
---

# Document Review

Improve ideation or brief documents through structured review.

## Step 1: Get the Document

**If a document path is provided:** Read it, then proceed to Step 2.

**If no document is specified:** Ask which document to review, or look for the most recent ideation/brief in `docs/ideations/` or `docs/briefs/`.

## Step 2: Assess

Read through the document and ask:

- What is unclear?
- What is unnecessary?
- What decision is being avoided?
- What assumptions are unstated?
- Where could scope accidentally expand (multiple audiences, multiple CTAs, multiple channels)?

These questions surface issues. Don't fix yet — just note what you find.

## Step 3: Evaluate

Score the document against these criteria:

| Criterion | What to Check |
|-----------|---------------|
| **Clarity** | Problem statement is clear, no vague language ("probably," "consider," "try to") |
| **Completeness** | Required sections present, constraints stated, open questions flagged |
| **Specificity** | Concrete enough for next step (ideation → can brief, brief → can create) |
| **YANGI** | No hypothetical audiences or channels, simplest approach chosen, single core message |

If invoked within a workflow (after `/workflows:ideate` or `/workflows:brief`), also check:
- **User intent fidelity** — Document reflects what was discussed, assumptions validated

## Step 4: Identify the Critical Improvement

Among everything found in Steps 2-3, does one issue stand out? If something would significantly improve the document's quality, this is the "must address" item. Highlight it prominently.

## Step 5: Make Changes

Present findings, then:

1. **Auto-fix** minor issues (vague language, formatting) without asking
2. **Ask approval** before substantive changes (restructuring, removing sections, changing meaning)
3. **Update** the document inline — no separate files, no metadata sections

### Simplification Guidance

Simplification is purposeful removal of unnecessary complexity, not shortening for its own sake.

**Simplify when:**
- Content serves hypothetical future audiences or channels, not current ones
- Sections repeat information already covered elsewhere
- Detail exceeds what's needed to take the next step
- Multiple CTAs or messages compete where one should win

**Don't simplify:**
- Constraints or edge cases that affect execution
- Rationale that explains why alternatives were rejected
- Open questions that need resolution before briefing or production

## Step 6: Offer Next Action

After changes are complete, ask:

1. **Refine again** - Another review pass
2. **Review complete** - Document is ready

### Iteration Guidance

After 2 refinement passes, recommend completion — diminishing returns are likely. But if the user wants to continue, allow it.

Return control to the caller (workflow or user) after selection.

## What NOT to Do

- Do not rewrite the entire document
- Do not add new sections or requirements the user didn't discuss
- Do not add audiences, channels, or assets beyond what was agreed
- Do not create separate review files or add metadata sections
