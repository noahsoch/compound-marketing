# Changelog

All notable changes to compound-marketing are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).
Versioning follows [Semantic Versioning](https://semver.org/).

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
