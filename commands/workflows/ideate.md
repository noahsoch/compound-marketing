---
name: workflows:ideate
description: Explore campaign ideas, content angles, and messaging hooks through collaborative dialogue before briefing
argument-hint: "[campaign idea or content concept to explore]"
---

# Ideate a Campaign or Content Concept

**Note: The current year is 2026.** Use this when dating ideation documents.

Ideation helps answer **WHAT** to do through collaborative dialogue. It precedes `/workflows:brief`, which answers **HOW** to structure and execute it.

**Process knowledge:** Load the `brainstorming` skill for detailed question techniques, approach exploration patterns, and YANGI principles.

## Campaign Description

<campaign_description> #$ARGUMENTS </campaign_description>

**If the description above is empty, ask the user:** "What would you like to explore? Describe the campaign idea, content concept, or marketing problem you're thinking about."

Do not proceed until you have a campaign description from the user.

## Execution Flow

### Phase 0: Assess Requirements Clarity

Evaluate whether ideation is needed based on the description.

**Clear requirements indicators:**
- Specific audience segment, channel, and goal provided
- Referenced existing campaigns to follow
- Described exact expected output
- Constrained, well-defined scope

**If requirements are already clear:**
Use **AskUserQuestion tool** to suggest: "Your requirements seem detailed enough to proceed directly to briefing. Should I run `/workflows:brief` instead, or would you like to explore the idea further?"

### Phase 1: Understand the Idea

#### 1.1 Swipe File Research (Lightweight — Always Runs)

Run these agents **in parallel** to surface relevant institutional knowledge:

- Task swipe-file-researcher("Find relevant past insights and audience learnings for: <campaign_description>")
- Task audience-researcher("Surface any known audience insights relevant to: <campaign_description>")

Focus on: audience language patterns, proven angles, what has worked in this space.

#### 1.2 Collaborative Dialogue

Use the **AskUserQuestion tool** to ask questions **one at a time**.

**Guidelines (see `brainstorming` skill for detailed techniques):**
- Prefer multiple choice when natural options exist
- Start broad (goal, audience) then narrow (channel, format, constraints)
- Validate assumptions explicitly
- Ask about success criteria

**Key questions to cover (one at a time):**
1. Who specifically is this for? (audience segment)
2. What one action do we want them to take?
3. What makes this timely or interesting right now?
4. What channel are we thinking?
5. What does success look like in 30 days?

**Exit condition:** Continue until the idea is clear OR user says "proceed"

### Phase 2: Explore Angles

Propose **2-3 concrete campaign angles** based on research and conversation.

For each angle, provide:
- Core message (the single most important thing we want them to know/feel/do)
- Hook (what makes someone stop and pay attention)
- Pros and cons
- Best channel fit

Lead with your recommendation and explain why. Apply YANGI — the simplest angle that achieves the goal wins.

Use **AskUserQuestion tool** to ask which angle the user prefers.

### Phase 3: Capture the Ideation

Write an ideation document to `docs/ideations/YYYY-MM-DD-<topic>-ideation.md`.

Ensure `docs/ideations/` directory exists before writing.

**Document structure (see `brainstorming` skill for the template format):**

```markdown
---
date: YYYY-MM-DD
topic: <kebab-case-topic>
audience: <specific segment>
channel: <primary channel>
---

# <Campaign Idea Title>

## The Idea
[1-2 paragraph description of the campaign concept]

## Target Audience
[Who and why they care — use their language, not marketing speak]

## Core Message
[The single most important thing we want them to know/feel/do]

## Chosen Angle
[Which of the 2-3 angles was chosen and why]

## Open Questions
[What needs to be resolved before briefing]

## Next Steps
→ `/workflows:brief` to structure the full brief
```

### Phase 4: Handoff

Use **AskUserQuestion tool** to present next steps:

**Question:** "Ideation captured. What would you like to do next?"

**Options:**
1. **Review and refine** - Improve the document through structured self-review
2. **Proceed to briefing** - Run `/workflows:brief` (will auto-detect this ideation)
3. **Done for now** - Return later

**If user selects "Review and refine":**

Load the `document-review` skill and apply it to the ideation document.

When document-review returns "Review complete", present next steps:
1. **Move to briefing** - Continue to `/workflows:brief`
2. **Done for now** - Ideation complete. To start briefing later: `/workflows:brief [document-path]`

## Output Summary

When complete, display:

```
Ideation complete!

Document: docs/ideations/YYYY-MM-DD-<topic>-ideation.md

Key decisions:
- Audience: [segment]
- Core message: [one sentence]
- Chosen angle: [angle name]

Next: Run `/workflows:brief` when ready to structure the campaign.
```

## Important Guidelines

- **Stay focused on WHAT, not HOW** - Production details belong in the brief
- **Ask one question at a time** - Don't overwhelm
- **Apply YANGI** - Prefer simpler, single-focus angles
- **Keep outputs concise** - 200-300 words per section max

NEVER PRODUCE ASSETS! Just explore and document decisions.
