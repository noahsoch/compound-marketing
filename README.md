# compound-marketing

AI-powered marketing tools for solo marketers and founders. 18 agents, 8 workflow commands, and 9 skills for the complete campaign loop: ideate → brief → create → review → push → report, with swarm execution, campaign folder organization, A/B testing, event research, funnel review, campaign calendar, and platform push.

## The Workflow Loop

```
ideate → brief ──────────────────────── create → review → push → report
  │         │                              ↑         │       │
  │         ├→ event-context-researcher    │ (swarm) │       └→ push-log.md
  │         ├→ icp-researcher              │         └→ funnel-reviewer
  │         └→ campaign-calendar           │           metric-reviewer
  │                                        │
  └→ [campaign folder: campaigns/{slug}/] ─┘
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
| `/workflows:ideate` | Explore campaign ideas and angles; optionally creates a named campaign folder | `/workflows:brainstorm` |
| `/workflows:brief` | Transform ideas into structured briefs with parallel event + ICP research; generates campaign calendar | `/workflows:plan` |
| `/workflows:create` | Produce content and assets from a brief; swarm mode for 3+ deliverable types | `/workflows:work` |
| `/workflows:review` | Multi-specialist asset review with funnel + metric alignment review for campaign folders | `/workflows:review` |
| `/workflows:push` | Format and push approved assets to Google Ads, Meta, Mailchimp, and Outlook — confirm before every push | — |
| `/workflows:report` | Capture winning copy and audience insights | `/workflows:compound` |
| `/workflows:design` | Generate Figma landing pages, static ads, and posters from a brief | — |
| `/workflows:design-reflect` | Capture design preference insights from manual Figma edits | — |

## Agents (18 total)

### Review Agents (9)

| Agent | Description |
|-------|-------------|
| `copy-reviewer` | Clarity, persuasion, structure, and engagement — P1/P2/P3 findings with specific rewrites |
| `seo-reviewer` | Keyword usage, heading structure, meta descriptions, search intent alignment |
| `conversion-reviewer` | CTAs and landing pages using Clarity/Trust/Desire/Action framework |
| `brand-voice-reviewer` | Tone consistency against a provided voice guide — flags deviations with in-brand rewrites |
| `email-reviewer` | Subject lines, preheaders, structure, and deliverability signals |
| `ad-copy-reviewer` | Platform-specific: Google RSA specs, Meta character limits/hooks, LinkedIn tone |
| `figma-design-reviewer` | Visual hierarchy, whitespace, typography, color, and brand alignment — P1/P2/P3 with layer-level corrections |
| `funnel-consistency-reviewer` | Cross-asset promise→delivery chain, offer consistency matrix, A/B differentiation check, and asset inventory completeness |
| `metric-alignment-reviewer` | Verifies every CTA, trust signal, and friction point drives toward the brief's `target_metric` |

### Research Agents (3)

| Agent | Description |
|-------|-------------|
| `swipe-file-researcher` | Grep-first lookup of `docs/insights/` for proven patterns and audience language |
| `event-context-researcher` | Event dates, cultural hooks, competitor campaign landscape, and content timing windows — saves to `{campaign}/plan/event-research.md` |
| `icp-researcher` | Grep-first audience research: verbatim language, pain points, desires, channel habits — saves to `{campaign}/plan/icp-research.md` |

### Workflow Agents (6)

| Agent | Description |
|-------|-------------|
| `brief-writer` | Structures vague ideas into complete briefs through targeted one-at-a-time questioning |
| `figma-designer` | Plans and generates Figma designs from a brief with design system integration and design preference lookup |
| `design-reflector` | Identifies deltas between generated spec and current Figma state, asks why, synthesizes reusable design preference signals |
| `google-ads-formatter` | Formats ad copy into Google RSA payloads — validates character limits, expands headlines to 8–15, returns draft JSON |
| `meta-ads-formatter` | Formats ad copy into Meta Marketing API payloads — maps objective, validates 125-char limit, selects CTA enum, returns draft JSON |
| `mailchimp-formatter` | Formats email sequences into Mailchimp v3 payloads — detects A/B variants, structures variate campaigns, converts markdown→HTML, creates DRAFTS only |

## Skills (9 total)

| Skill | Description |
|-------|-------------|
| `brainstorming` | Explore campaign ideas before briefing — YANGI principles, one question at a time |
| `document-review` | Structured self-review of ideation and brief documents |
| `copywriting-frameworks` | AIDA, PAS, FAB, StoryBrand — when and how to use each |
| `brief-templates` | MINIMAL/STANDARD/COMPREHENSIVE brief templates for campaigns, content, ads, and email |
| `swipe-file` | The compounding knowledge system — capture, tag, and retrieve marketing insights |
| `visual-design-principles` | Layout hierarchy, typography scale, and color contrast reference for marketing design |
| `campaign-folders` | Campaign folder structure: `campaigns/{slug}/plan\|assets\|reviews\|calendar\|reports/` — context detection and backward-compatible output routing |
| `ab-testing` | A/B variant generation framework — what to vary, file naming, required frontmatter, single-variable vs multi-concept strategies |
| `campaign-calendar` | Backward-planned T-minus timeline with per-milestone commands; includes Outlook push script via Microsoft Graph API |

## MCP Servers (3 total)

| Server | Description |
|--------|-------------|
| `context7` | Framework and library documentation lookup |
| `figma` | Access Figma design files, generate frames, read design system tokens — OAuth auth required |
| `canva` | Access Canva designs, templates, and brand assets via AI Connector — OAuth auth required |

## Output Directories

**Campaign-scoped (created by `/workflows:ideate` when a slug is provided):**

```
campaigns/{slug}/
├── plan/           ← ideation.md, brief.md, event-research.md, icp-research.md
├── assets/
│   ├── ads/        ← from /workflows:create (ad-copy-{concept}-a.md, etc.)
│   ├── emails/     ← from /workflows:create
│   └── landing-pages/
├── reviews/        ← from /workflows:review
├── calendar/       ← campaign-calendar.md from /workflows:brief
└── reports/        ← push-log.md from /workflows:push
```

**Global (flat, backward-compatible):**

```
docs/
├── ideations/       ← from /workflows:ideate (no campaign slug)
├── briefs/          ← from /workflows:brief (no campaign folder)
├── assets/          ← from /workflows:create and /workflows:design (design specs)
├── reviews/         ← from /workflows:review (single-asset reviews)
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
# Full campaign workflow (with campaign folder)
/workflows:ideate "F1 weekend ticket campaign targeting motorsport enthusiasts"
# → Prompts for campaign slug: f1-weekend-2026
# → Creates campaigns/f1-weekend-2026/ folder structure

/workflows:brief campaigns/f1-weekend-2026/plan/ideation.md
# → Runs event-context-researcher + icp-researcher in parallel
# → Generates campaign-calendar.md (event date detected)
# → Optionally pushes calendar to Outlook

/workflows:create campaigns/f1-weekend-2026/plan/brief.md
# → Swarm mode: offer foundation → parallel copy workers → parallel Figma workers
# → A/B variants generated for every major asset

/workflows:review campaigns/f1-weekend-2026/
# → All specialist reviewers + funnel-consistency + metric-alignment in parallel
# → Findings written to campaigns/f1-weekend-2026/reviews/

/workflows:push campaigns/f1-weekend-2026/
# → Formats for Google Ads, Meta, Mailchimp
# → Shows each payload, requires confirmation before pushing

/workflows:report "The urgency angle won — 23% CTR vs 14% for social proof variant"
# → Captures to docs/insights/ for next campaign

# Design loop (Figma MCP required)
/workflows:design campaigns/f1-weekend-2026/plan/brief.md
# [edit the design in Figma]
/workflows:design-reflect https://www.figma.com/file/...
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
