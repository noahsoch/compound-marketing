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
├── review/     # Marketing asset review agents
├── research/   # Audience and market research agents
└── workflow/   # Brief writing and planning agents

commands/
└── workflows/  # Core workflow commands (workflows:ideate, workflows:brief, etc.)

skills/
└── */          # All skills with SKILL.md and optional references/
```

## Command Naming Convention

**Workflow commands** use `workflows:` prefix to avoid collisions with built-in commands:
- `/workflows:ideate` - Explore campaign ideas
- `/workflows:brief` - Create marketing briefs
- `/workflows:create` - Produce content and assets
- `/workflows:review` - Review assets with specialist agents
- `/workflows:report` - Capture insights and winning copy

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
ideate → brief → create → review → report
  ↑                                    ↓
  └────────── (swipe file) ────────────┘
```

Each `/workflows:report` run adds entries to `docs/insights/` that future `/workflows:ideate` and `/workflows:brief` runs can surface via the `swipe-file-researcher` agent. This is the compounding flywheel.
