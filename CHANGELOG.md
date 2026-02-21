# Changelog

All notable changes to compound-marketing are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).
Versioning follows [Semantic Versioning](https://semver.org/).

---

## [1.3.0] — 2026-02-21

### Added

**Workflow Commands (1)**
- `workflows:push` — Final-step platform push command. Reads a campaign folder, detects configured integrations (Google Ads, Meta Ads, Mailchimp, Outlook), formats payloads via specialist formatter agents, shows each payload for review, and executes only after explicit confirmation. NEVER auto-executes. Logs all results to `{campaign}/reports/push-log.md`.

**Workflow Agents (3)**
- `google-ads-formatter` — Formats approved ad copy into Google Ads RSA payloads. Validates all character limits (headlines ≤30 chars, descriptions ≤90 chars), expands sparse headline sets to 8–15, and returns draft JSON (always `status: PAUSED`).
- `meta-ads-formatter` — Formats approved ad copy into Meta Marketing API payloads. Maps `target_metric` to Meta campaign objectives, validates primary text against 125-char recommended limit, selects correct CTA button type from Meta's enum, and returns draft JSON (always `status: PAUSED`).
- `mailchimp-formatter` — Formats approved email sequences into Mailchimp API v3 payloads. Detects A/B variants from filename convention and structures them as variate campaigns (50/50 subject-line split, open-rate winner after 4 hours). Converts markdown to HTML. Always creates DRAFTS — never schedules sends.

**Review Agents (2)**
- `funnel-consistency-reviewer` — Cross-asset funnel integrity review. Checks the promise→delivery chain (ad→LP, email→LP), offer consistency matrix across all touchpoints, A/B variant differentiation quality, and asset inventory completeness against the brief. P1/P2/P3 findings.
- `metric-alignment-reviewer` — Verifies every asset drives toward the brief's `target_metric`. Reviews primary CTA alignment, friction in the conversion path, required trust signals, urgency consistency, and email sequence logic. P1/P2/P3 findings.

**Research Agents (2)**
- `event-context-researcher` — Researches event-specific context: dates, format, location, cultural hooks, relevant cultural moments, competitor campaign landscape, and content timing windows. Saves to `{campaign}/plan/event-research.md`. Runs in parallel with `icp-researcher` during `/workflows:brief`.
- `icp-researcher` — Audience research using a Grep-first pattern (checks `docs/insights/audience-observations/` before external search). Surfaces verbatim audience language, pain points, desires, and channel habits. Saves to `{campaign}/plan/icp-research.md`.

**Skills (3)**
- `campaign-folders` — Campaign folder structure convention (`campaigns/{slug}/plan|assets|reviews|calendar|reports/`). Defines slug format, folder creation commands, and campaign context detection logic for all workflow commands. Backward compatible with flat `docs/` directories.
- `ab-testing` — A/B variant generation framework. Defines what to vary by asset type, single-variable vs multi-concept strategies, required frontmatter per variant file (`variant`, `test_variable`, `hypothesis`), file naming convention, and expected output counts for a full campaign.
- `campaign-calendar` — Backward-planned campaign timeline skill. T-minus framework from T−6 weeks (ideation) through T+3 days (post-campaign report). Each milestone includes the exact workflow command to run, what to do, timing, and output path. Includes Outlook push script (`scripts/push-to-outlook.js`) that reads milestone dates from calendar markdown and posts to Microsoft Graph API.

### Modified

**Workflow Commands (4)**
- `workflows:ideate` — Added campaign folder setup step (Phase 3): asks for campaign slug, creates full campaign directory structure (`campaigns/{slug}/plan|assets|reviews|calendar|reports/`), and saves ideation to `{campaign}/plan/ideation.md` when a slug is provided.
- `workflows:brief` — Added campaign folder detection, parallel `event-context-researcher` + `icp-researcher` launch (alongside swipe-file-researcher), required frontmatter fields (`target_metric`, `test_strategy`, `event_date`, `cta_destination`), and automatic campaign calendar generation after brief approval when `event_date` is present.
- `workflows:create` — Rewritten with swarm-based execution. Mode selector: 3+ deliverable types → swarm mode (A1: synchronous offer foundation → A2: parallel copy workers → A3: parallel Figma workers); 1–2 types → sequential mode. Integrates `campaign-folders` skill for output directory detection and `ab-testing` skill for A/B variant generation on every asset.
- `workflows:review` — Added `funnel-consistency-reviewer` and `metric-alignment-reviewer` to the parallel review swarm for campaign-folder reviews. Reviews output now written to `{campaign}/reviews/` when campaign folder is detected. Summary includes funnel integrity and metric readiness status.

### Output Directories

New campaign-scoped structure created by `/workflows:ideate` when a campaign slug is provided:
```
campaigns/{slug}/
├── plan/           ← ideation.md, brief.md, event-research.md, icp-research.md
├── assets/         ← from /workflows:create (ads/, emails/, landing-pages/)
├── reviews/        ← from /workflows:review
├── calendar/       ← campaign-calendar.md from /workflows:brief
└── reports/        ← push-log.md from /workflows:push, post-campaign-report.md
```

---

## [1.2.0] — 2026-02-21

### Added

**Workflow Commands (2)**
- `workflows:design` — Generate Figma landing pages, static ads, and posters from a marketing brief. Reads existing design system tokens if a Figma URL is provided; bootstraps design system tokens for new files; runs `figma-design-reviewer` automatically; stores a complete spec in `docs/assets/` for future reflection.
- `workflows:design-reflect` — Capture design preference insights after manually editing a Figma design. Reads current Figma state via MCP, compares to the original generated spec, asks the user why they changed things (one question at a time), and documents the preference signal in `docs/insights/design-learnings/`.

**Workflow Agents (2)**
- `figma-designer` — Plans design structure (sections, hierarchy, typography, color) from a brief and design system context, checks `docs/insights/design-learnings/` for prior preferences, then generates the Figma frame via `generate_figma_design`. Returns a complete design spec.
- `design-reflector` — Identifies structural deltas between the generated spec and the current Figma state; asks targeted questions to extract user reasoning; synthesizes each answer into a reusable design preference signal for future design runs.

**Review Agents (1)**
- `figma-design-reviewer` — Reviews generated Figma designs through five lenses: hierarchy, whitespace, typography, color contrast, and brand alignment. P1/P2/P3 findings with specific layer-level corrections and concrete values.

**Skills (1)**
- `visual-design-principles` — Reference skill for layout hierarchy, typography scale, and color contrast in marketing design. Used by `figma-designer` and `figma-design-reviewer`. Includes three reference documents: layout-hierarchy.md, typography-scale.md, color-contrast.md.

**Schema Extensions**
- `skills/swipe-file/schema.yaml` — Added `asset_type` enums: `figma_frame`, `landing_page_design`, `ad_design`, `poster_design`. Added `performance_signal` enums: `design_preference`, `user_edited`. Added `design-learnings` category mappings.
- `skills/swipe-file/references/yaml-schema.md` — Documented all design schema additions with examples, filename conventions, and optional fields (`figma_url`, `original_spec_path`).

**Output Directory**
- `docs/insights/design-learnings/` — New category for visual preference insights captured by `workflows:design-reflect`.

### Fixed
- `plugin.json` description corrected from "14 agents" to "11 agents" (actual count: 8 existing + 3 new)

---

## [1.1.0] — 2026-02-20

### Added

**MCP Servers (2)**
- `figma` — Figma remote MCP (`https://mcp.figma.com/mcp`). Access design files, components, and assets directly in marketing sessions. Authenticates via OAuth.
- `canva` — Canva AI Connector MCP (`https://mcp.canva.com/mcp` via `mcp-remote`). Access your Canva designs, templates, and brand assets. Authenticates via OAuth. Requires Node.js 22+.

---

## [1.0.0] — 2026-02-20

### Added

**Workflow Commands (5)**
- `workflows:ideate` — Explore campaign ideas through collaborative dialogue before briefing
- `workflows:brief` — Transform ideas into structured marketing briefs (MINIMAL/STANDARD/COMPREHENSIVE)
- `workflows:create` — Produce marketing assets from a brief with built-in copy review
- `workflows:review` — Multi-specialist asset review with P1/P2/P3 findings written to `docs/reviews/`
- `workflows:report` — Two-phase parallel orchestration to capture insights to `docs/insights/`

**Review Agents (6)**
- `copy-reviewer` — Clarity, persuasion, structure, engagement — specific rewrites with severity ratings
- `seo-reviewer` — On-page SEO: keywords, headings, meta descriptions, search intent
- `conversion-reviewer` — CTAs and landing pages: Clarity/Trust/Desire/Action framework
- `brand-voice-reviewer` — Tone and vocabulary consistency against a provided voice guide
- `email-reviewer` — Subject lines, preheaders, structure, deliverability signals
- `ad-copy-reviewer` — Platform-specific: Google RSA, Meta, LinkedIn

**Research Agents (1)**
- `swipe-file-researcher` — Grep-first lookup of `docs/insights/` using Haiku model

**Workflow Agents (1)**
- `brief-writer` — Structures vague ideas into complete briefs through targeted questioning

**Skills (5)**
- `brainstorming` — Marketing adaptation of compound-engineering brainstorming skill
- `document-review` — Structured review for ideation and brief documents
- `copywriting-frameworks` — AIDA, PAS, FAB, StoryBrand with reference docs
- `brief-templates` — MINIMAL/STANDARD/COMPREHENSIVE templates for campaigns, content, ads, email
- `swipe-file` — Compounding knowledge system with YAML schema and capture patterns

**MCP Servers**
- Context7 — Framework documentation lookup (carries over from compound-engineering)
