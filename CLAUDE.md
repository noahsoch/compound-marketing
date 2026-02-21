# Compound Marketing Plugin Development

## Versioning Requirements

**IMPORTANT**: Every change to this plugin MUST include updates to all three files:

1. **`.claude-plugin/plugin.json`** - Bump version using semver
2. **`CHANGELOG.md`** - Document changes using Keep a Changelog format
3. **`README.md`** - Verify/update component counts and tables

### Version Bumping Rules

- **MAJOR** (1.0.0 → 2.0.0): Breaking changes, major reorganization
- **MINOR** (1.0.0 → 1.1.0): New agents, commands, or skills
- **PATCH** (1.0.0 → 1.0.1): Bug fixes, doc updates, minor improvements

### Pre-Commit Checklist

Before committing ANY changes:

- [ ] Version bumped in `.claude-plugin/plugin.json`
- [ ] CHANGELOG.md updated with changes
- [ ] README.md component counts verified
- [ ] README.md tables accurate (agents, commands, skills)
- [ ] plugin.json description matches current counts

### Directory Structure

```
agents/
├── review/     # Marketing asset review agents (9)
├── research/   # Audience and market research agents (3)
└── workflow/   # Brief writing, design, and platform formatter agents (6)

commands/
└── workflows/  # Core workflow commands (8 total)

skills/
└── */          # All skills with SKILL.md and optional references/ (9 active)

campaigns/      # Created at runtime by /workflows:ideate when a slug is provided
└── {slug}/
    ├── plan/       # ideation.md, brief.md, event-research.md, icp-research.md
    ├── assets/     # ads/, emails/, landing-pages/
    ├── reviews/    # findings from /workflows:review
    ├── calendar/   # campaign-calendar.md
    └── reports/    # push-log.md, post-campaign-report.md
```

## Command Naming Convention

**Workflow commands** use `workflows:` prefix to avoid collisions with built-in commands:
- `/workflows:ideate` - Explore campaign ideas; optionally creates campaign folder
- `/workflows:brief` - Create marketing briefs with parallel research; generates calendar
- `/workflows:create` - Produce content and assets (swarm mode for 3+ deliverable types)
- `/workflows:review` - Review assets with specialist + funnel + metric agents
- `/workflows:push` - Format and push approved assets to external platforms
- `/workflows:report` - Capture insights and winning copy
- `/workflows:design` - Generate Figma designs from a brief
- `/workflows:design-reflect` - Capture design preference insights from Figma edits

**Why `workflows:`?** Claude Code has built-in `/plan` and `/review` commands. Using `name: workflows:ideate` in frontmatter creates a unique `/workflows:ideate` command with no collision.

## Skill Compliance Checklist

When adding or modifying skills, verify compliance:

### YAML Frontmatter (Required)

- [ ] `name:` present and matches directory name (lowercase-with-hyphens)
- [ ] `description:` present and describes **what it does and when to use it**

### Reference Links (Required if references/ exists)

- [ ] All files in `references/` are linked as `[filename.md](./references/filename.md)`
- [ ] No bare backtick references like `` `references/file.md` `` - use proper markdown links

### Writing Style

- [ ] Use imperative/infinitive form (verb-first instructions)
- [ ] Avoid second person ("you should") - use objective language

### Quick Validation Command

```bash
# Check for unlinked references in a skill
grep -E '`(references|assets)/[^`]+`' skills/*/SKILL.md
# Should return nothing if all refs are properly linked

# Check description format - should describe what + when
grep -E '^description:' skills/*/SKILL.md
```

## Marketing-Specific Conventions

### Protected Artifacts

The following paths are compound-marketing pipeline artifacts and must never be flagged for deletion by any review agent:

- `docs/ideations/*.md` — Ideation docs created by `/workflows:ideate`
- `docs/briefs/*.md` — Brief files created by `/workflows:brief`
- `docs/insights/*.md` — Insight docs created by `/workflows:report`

### Swipe File Schema

The swipe-file skill uses `schema.yaml` for YAML frontmatter validation.
When adding insights, validate against:
- `asset_type` enum values
- `channel` enum values
- `performance_signal` enum values

Reference: `skills/swipe-file/references/yaml-schema.md`

### Workflow Loop

```
ideate → brief → create → review → push → report
  │         │       ↑ (swarm)  │               ↓
  │         ├→ event-researcher │               ↓
  │         ├→ icp-researcher   └→ funnel-rev   ↓
  │         └→ campaign-calendar   metric-rev   ↓
  └→ [campaigns/{slug}/]                        ↓
  ↑                                             ↓
  └──────────────── (swipe file) ───────────────┘
```

Each `/workflows:report` run adds entries to `docs/insights/` that future runs can surface via `swipe-file-researcher`. Each design edit captured by `/workflows:design-reflect` adds to `docs/insights/design-learnings/`. Knowledge compounds across both copy and visual tracks.

### Key Downstream Dependencies

- `target_metric` in brief frontmatter → required by `metric-alignment-reviewer`
- `test_strategy` in brief frontmatter → required by `ab-testing` skill in `/workflows:create`
- `event_date` in brief frontmatter → triggers calendar generation in `/workflows:brief`
- `campaign_folder` in brief frontmatter → routes outputs for all downstream commands
- `outlook_synced` in calendar frontmatter → checked by `/workflows:push` before Outlook sync
