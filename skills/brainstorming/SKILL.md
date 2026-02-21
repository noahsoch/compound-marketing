---
name: brainstorming
description: Guides exploring campaign ideas, audience insights, and strategic decisions before briefing. Use before implementing campaigns, creating content series, or making significant marketing decisions.
---

# Brainstorming

This skill provides detailed process knowledge for effective brainstorming sessions that clarify **WHAT** to do before diving into **HOW** to do it.

## When to Use This Skill

Brainstorming is valuable when:
- A campaign idea is vague or exploratory
- Multiple angles could work and trade-offs need exploring
- The audience or channel hasn't been defined
- The user hasn't fully articulated what they want
- The campaign scope needs refinement before a brief is written

Brainstorming can be skipped when:
- Campaign requirements are explicit and detailed
- The user knows exactly what they want to create
- The task is a straightforward one-off asset with clear specs

## Core Process

### Phase 0: Assess Requirement Clarity

Before diving into questions, assess whether brainstorming is needed.

**Signals that requirements are clear:**
- User provided specific audience segment, channel, and goal
- User referenced existing campaigns to follow
- User described exact output they want
- Scope is constrained and well-defined

**Signals that brainstorming is needed:**
- User used vague terms ("something for our newsletter", "a campaign for Q2")
- Multiple reasonable interpretations exist
- Audience or channel hasn't been discussed
- User seems unsure about the angle

If requirements are clear, suggest: "Your requirements seem clear. Consider proceeding directly to briefing."

### Phase 1: Understand the Idea

Ask questions **one at a time** to understand the user's intent. Never ask multiple questions at once.

**Question Techniques:**

1. **Prefer multiple choice when natural options exist**
   - Good: "Who is this for: (a) existing customers, (b) new prospects, or (c) both?"
   - Avoid: "Who is your audience?"

2. **Start broad, then narrow**
   - First: What is the goal or opportunity?
   - Then: Who is the audience?
   - Finally: What channel and format?

3. **Validate assumptions explicitly**
   - "I'm assuming this is for email. Is that correct?"

4. **Ask about success criteria early**
   - "What does success look like for this in 30 days?"

**Key Topics to Explore:**

| Topic | Example Questions |
|-------|-------------------|
| Goal | What action do we want the reader to take? What changes if this works? |
| Audience | Who specifically is this for? What do they care about? |
| Angle | What makes this timely or interesting right now? |
| Channel | Where will this live? Email, social, blog, ads? |
| Constraints | Any deadlines, budget limits, or brand rules? |
| Success | How will you know this worked? |

**Exit Condition:** Continue until the idea is clear OR user says "proceed" or "let's move on"

### Phase 2: Explore Approaches

After understanding the idea, propose 2-3 concrete campaign angles.

**Structure for Each Approach:**

```markdown
### Angle A: [Name]

[2-3 sentence description of the angle and its core message]

**Pros:**
- [Benefit 1]
- [Benefit 2]

**Cons:**
- [Drawback 1]

**Best when:** [Circumstances where this angle shines]
```

**Guidelines:**
- Lead with a recommendation and explain why
- Be honest about trade-offs
- Apply YANGI — You Aren't Gonna Need It. The simplest campaign that achieves the goal wins.
- Reference past swipe file insights when relevant

### Phase 3: Capture the Design

Summarize key decisions in a structured format.

**Design Doc Structure:**

```markdown
---
date: YYYY-MM-DD
topic: <kebab-case-topic>
audience: <segment>
channel: <primary channel>
---

# <Campaign Idea Title>

## The Idea
[1-2 paragraph description]

## Target Audience
[Who and why they care]

## Core Message
[The single most important thing we want them to know/feel/do]

## Chosen Angle
[Which angle was chosen and why]

## Open Questions
[What needs to be resolved before briefing]

## Next Steps
→ `/workflows:brief` to structure the full brief
```

**Output Location:** `docs/ideations/YYYY-MM-DD-<topic>-ideation.md`

Ensure `docs/ideations/` directory exists before writing.

### Phase 4: Handoff

Present clear options for what to do next:

1. **Proceed to briefing** → Run `/workflows:brief`
2. **Refine further** → Continue exploring the idea
3. **Done for now** → User will return later

## YANGI Principles

During brainstorming, actively resist complexity:

- **Don't design for hypothetical future audiences**
- **Choose the simplest angle that achieves the stated goal**
- **Prefer one clear message over multiple competing messages**
- **Ask "Do we really need this?" when scope expands**
- **Defer channel decisions that don't need to be made now**

## Incremental Validation

Keep outputs short — 200-300 words per section. After each section, pause to validate:

- "Does this match what you had in mind?"
- "Any adjustments before we continue?"

## Anti-Patterns to Avoid

| Anti-Pattern | Better Approach |
|--------------|-----------------|
| Asking 5 questions at once | Ask one at a time |
| Jumping to copy and assets | Stay focused on WHAT, not HOW |
| Proposing campaigns for every channel | Pick the best channel first |
| Ignoring past swipe file insights | Check docs/insights/ for relevant learnings |
| Making assumptions about the audience | State assumptions explicitly and confirm |
| Writing long ideation docs | Keep it concise — details go in the brief |

## Integration with Briefing

Brainstorming answers **WHAT** to do:
- Campaign idea and angle
- Audience and channel
- Core message and goal

Briefing answers **HOW** to structure and execute it:
- Audience specifics and messaging hierarchy
- Channel-specific guidance
- Asset list and success metrics

When an ideation doc exists, `/workflows:brief` should detect it and use it as input.
