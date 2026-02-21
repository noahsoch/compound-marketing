# compound-marketing

AI-powered marketing tools for solo marketers and founders. 11 agents, 7 workflow commands, and 8 skills for the complete ideate → brief → create → review → report loop, plus Figma design generation and design-learning.

## The Workflow Loop

```
ideate → brief → create ────────────────────── review → report
                    └→ design → [edit in Figma]
                                    └→ design-reflect → docs/insights/design-learnings/
  ↑                                                              ↓
  └──────────────────────── (swipe file) ───────────────────────┘
```

Each `/workflows:report` run captures copy insights to `docs/insights/`. Each `/workflows:design-reflect` run captures visual preferences to `docs/insights/design-learnings/`. Future runs surface both automatically. Knowledge compounds.

## Installation

Install via the Claude Code plugin marketplace, or clone and install locally:

```bash
git clone https://github.com/yourorg/compound-marketing-plugin
```

## Workflow Commands

| Command | Description | Engineering Equivalent |
|---------|-------------|------------------------|
| `/workflows:ideate` | Explore campaign ideas and angles through collaborative dialogue | `/workflows:brainstorm` |
| `/workflows:brief` | Transform ideas into structured marketing briefs | `/workflows:plan` |
| `/workflows:create` | Produce content and assets from a brief | `/workflows:work` |
| `/workflows:review` | Multi-specialist asset review with P1/P2/P3 findings | `/workflows:review` |
| `/workflows:report` | Capture winning copy and audience insights | `/workflows:compound` |
| `/workflows:design` | Generate Figma landing pages, static ads, and posters from a brief | — |
| `/workflows:design-reflect` | Capture design preference insights from manual Figma edits | — |

## Agents (11 total)

### Review Agents

| Agent | Description |
|-------|-------------|
| `copy-reviewer` | Clarity, persuasion, structure, and engagement — P1/P2/P3 findings with specific rewrites |
| `seo-reviewer` | Keyword usage, heading structure, meta descriptions, search intent alignment |
| `conversion-reviewer` | CTAs and landing pages using Clarity/Trust/Desire/Action framework |
| `brand-voice-reviewer` | Tone consistency against a provided voice guide — flags deviations with in-brand rewrites |
| `email-reviewer` | Subject lines, preheaders, structure, and deliverability signals |
| `ad-copy-reviewer` | Platform-specific: Google RSA specs, Meta character limits/hooks, LinkedIn tone |
| `figma-design-reviewer` | Visual hierarchy, whitespace, typography, color, and brand alignment — P1/P2/P3 with layer-level corrections |

### Research Agents

| Agent | Description |
|-------|-------------|
| `swipe-file-researcher` | Grep-first lookup of `docs/insights/` for proven patterns and audience language |

### Workflow Agents

| Agent | Description |
|-------|-------------|
| `brief-writer` | Structures vague ideas into complete briefs through targeted one-at-a-time questioning |
| `figma-designer` | Plans and generates Figma designs from a brief with design system integration and design preference lookup |
| `design-reflector` | Identifies deltas between generated spec and current Figma state, asks why, synthesizes reusable design preference signals |

## Skills (8 total)

| Skill | Description |
|-------|-------------|
| `brainstorming` | Explore campaign ideas before briefing — YANGI principles, one question at a time |
| `document-review` | Structured self-review of ideation and brief documents |
| `copywriting-frameworks` | AIDA, PAS, FAB, StoryBrand — when and how to use each |
| `brief-templates` | MINIMAL/STANDARD/COMPREHENSIVE brief templates for campaigns, content, ads, and email |
| `swipe-file` | The compounding knowledge system — capture, tag, and retrieve marketing insights |
| `visual-design-principles` | Layout hierarchy, typography scale, and color contrast reference for marketing design |
| `analytics-interpretation` | *(Phase 3 — coming soon)* |
| `campaign-planning` | *(Phase 3 — coming soon)* |

## MCP Servers (3 total)

| Server | Description |
|--------|-------------|
| `context7` | Framework and library documentation lookup |
| `figma` | Access Figma design files, generate frames, read design system tokens — OAuth auth required |
| `canva` | Access Canva designs, templates, and brand assets via AI Connector — OAuth auth required |

## Output Directories

Created automatically at runtime:

```
docs/
├── ideations/       ← from /workflows:ideate
├── briefs/          ← from /workflows:brief
├── assets/          ← from /workflows:create and /workflows:design (design specs)
├── reviews/         ← from /workflows:review
└── insights/        ← from /workflows:report and /workflows:design-reflect
    ├── winning-headlines/
    ├── winning-emails/
    ├── winning-ads/
    ├── winning-copy/
    ├── audience-observations/
    ├── campaign-learnings/
    ├── failed-approaches/
    ├── design-learnings/    ← visual preference insights from /workflows:design-reflect
    └── patterns/
        └── critical-patterns.md
```

## Quick Start

```bash
# Copy workflow
/workflows:ideate "email campaign for churned SaaS founders"
/workflows:brief docs/ideations/2026-02-20-reactivation-campaign-ideation.md
/workflows:create docs/briefs/2026-02-20-email-saas-reactivation-brief.md
/workflows:review docs/assets/2026-02-20-reactivation-email.md
/workflows:report "the urgency subject line won the A/B test with 47% open rate"

# Design workflow (Figma MCP required)
/workflows:design docs/briefs/2026-02-21-email-saas-reactivation-brief.md
# [edit the design in Figma]
/workflows:design-reflect https://www.figma.com/file/... docs/assets/2026-02-21-design-landing-page-spec.md
```

## The Compounding Effect

The plugin's value compounds over time across two tracks:

**Copy track:** At campaign #1, you start from scratch. By campaign #10, the swipe-file-researcher has a library of proven patterns for your audience — and `ideate` and `brief` draw on them automatically.

**Design track:** The first time you generate a Figma design, the AI uses generic best practices. After one `design-reflect` session, your visual preferences are captured. By design #3, the `figma-designer` agent applies your preferences automatically — serif headlines for premium positioning, your exact CTA green, the hero height you always adjust. Manual corrections decrease with each iteration.

> "Each marketing campaign should make subsequent campaigns easier — not harder."

## Development

See [CLAUDE.md](./CLAUDE.md) for:
- Versioning requirements (every change bumps plugin.json, CHANGELOG.md, and README.md)
- Skill compliance checklist
- Protected artifact paths
- Workflow loop conventions
