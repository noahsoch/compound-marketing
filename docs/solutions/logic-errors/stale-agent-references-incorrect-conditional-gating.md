---
title: "Stale agent references and conditional gating bugs in Claude Code plugin commands"
date: 2026-02-21
type: logic-errors
tags: [claude-code-plugin, agent-references, command-consistency, marketing-plugin]
components: [plugin-commands, agent-registry, workflow-orchestration]
symptoms:
  - "Command files reference agents that don't exist"
  - "Agents conditionally invoked when they should always run"
  - "Frontmatter fields checked in validation but missing from template"
root_cause: "Agents designed in planning phase but only partially implemented; command files not updated when new agents replaced old references"
related_files:
  - commands/workflows/ideate.md
  - commands/workflows/brief.md
  - commands/workflows/report.md
severity: p2
status: resolved
---

# Stale Agent References and Incorrect Conditional Gating in Plugin Commands

## Problem Symptoms

After shipping compound-marketing v1.3.0, a code review surfaced three related classes of bugs in command `.md` files:

1. **Non-existent agent references** — `ideate.md` and `report.md` called `Task audience-researcher(...)`, an agent that was never created. The actual agent is `icp-researcher`.
2. **Wrong conditional gate** — `brief.md` gated `icp-researcher` behind "if campaign has a specific event" — but ICP research must run for every campaign. Only `event-context-researcher` is conditional.
3. **Frontmatter/checklist mismatch** — `brief.md`'s pre-delivery checklist required `cta_destination` in frontmatter, but the frontmatter template block didn't include the field.

## Root Cause Analysis

A planning-implementation gap. `audience-researcher` was named in the original design phase and copy-pasted into command files before the agent was built. The eventually-built agent was named `icp-researcher`. The command files retained the planning name; the divergence was never reconciled because there was no forcing function connecting planned names to implemented files.

The conditional gating bug had a different cause: when the `icp-researcher` was added to `brief.md` in v1.3.0, it was added under the same condition block as `event-context-researcher` (which is legitimately conditional). A copy-paste of the condition block captured both agents together.

The frontmatter/checklist mismatch: `cta_destination` was added to the checklist in one pass and added to downstream agent documentation, but the template block in the same file wasn't updated in the same commit.

## Discovery

The bugs were caught by grepping for referenced agent names and verifying they exist as files:

```bash
# Step 1: find all Task agent references across command files
grep -rh "Task " compound-marketing/commands/ | grep -v "^#"

# Step 2: verify audience-researcher exists
grep -r "audience-researcher" compound-marketing/agents/
# → No output. Agent does not exist.

# Step 3: find conditional gate
grep -n "icp-researcher" compound-marketing/commands/workflows/brief.md
# → Line shows it nested under an "if event" condition
```

## Solution

### Bug 1 — `ideate.md`: Remove non-existent `audience-researcher` task

At the ideation stage only `swipe-file-researcher` should run. Full ICP research runs later at brief stage.

**Before:**
```markdown
- Task swipe-file-researcher("Find relevant past insights for: <campaign_description>")
- Task audience-researcher("Surface any known audience insights relevant to: <campaign_description>")
```

**After:**
```markdown
- Task swipe-file-researcher("Find relevant past insights for: <campaign_description>")
```

### Bug 2 — `report.md`: Remove stale `audience-researcher` reference

**Before:**
```markdown
- **Audience insight** → audience-researcher enriches with additional context
```

**After:** Line removed entirely.

### Bug 3 — `brief.md`: Make `icp-researcher` unconditional

Every campaign has a target audience. Only `event-context-researcher` remains conditional.

**Before:**
```markdown
**If campaign has a specific event or the audience research would benefit from external context:**
- Task event-context-researcher(campaign_description + event details if known)
- Task icp-researcher(campaign_description + audience signals from ideation)
```

**After:**
```markdown
- Task icp-researcher(campaign_description + audience signals from ideation)

**Also run if campaign is tied to a specific event:**
- Task event-context-researcher(campaign_description + event details if known)
```

### Bug 4 — `brief.md`: Add `cta_destination` to frontmatter template

**Before:**
```yaml
---
campaign: {slug or omit}
target_metric: email_subscribers | revenue | sign_ups | attendance
test_strategy: single-variable | multi-concept
---
```

**After:**
```yaml
---
campaign: {slug or omit}
target_metric: email_subscribers | revenue | sign_ups | attendance
test_strategy: single-variable | multi-concept
cta_destination: https://...
---
```

### Summary

| Bug | File | Type | Fix |
|-----|------|------|-----|
| 1 | `ideate.md` | Non-existent agent call | Removed `Task audience-researcher(...)` entirely |
| 2 | `report.md` | Non-existent agent reference | Removed from optional specialist list |
| 3 | `brief.md` | Wrong conditional gate | Made `icp-researcher` unconditional; `event-context-researcher` stays conditional |
| 4 | `brief.md` | Template/checklist mismatch | Added `cta_destination` to frontmatter template |

## Prevention

### 1. Verification Command — Run Before Every Release

Cross-reference every agent name called in Task statements against actual agent `.md` files:

```bash
# From compound-marketing root:
# Find all Task calls in command files and check for matching agent files

grep -rh "Task " commands/ \
  | grep -oP "Task \K[a-z][a-z0-9-]+" \
  | sort -u \
  | while read agent; do
      if ls agents/**/${agent}.md 2>/dev/null | grep -q .; then
        echo "✓ ${agent}"
      else
        echo "✗ MISSING: ${agent} (referenced in commands but no agents/**/${agent}.md found)"
      fi
    done
```

Any `✗ MISSING` line blocks the release.

Reverse check — agent files with no command references (cleanup signal, not a blocker):

```bash
for f in agents/**/*.md; do
  agent=$(basename "$f" .md)
  if ! grep -rq "$agent" commands/; then
    echo "UNREFERENCED: ${agent}"
  fi
done
```

### 2. Pre-Release Checklist Addition for CLAUDE.md

Add to the pre-commit checklist in CLAUDE.md:

```
- [ ] Agent reference audit: run verification command — zero MISSING results required
- [ ] Conditional vs. always-run review: every agent in an `if` block is there intentionally;
      agents that must always run have no condition guard
- [ ] Template-checklist parity: every field in a checklist exists in the corresponding
      frontmatter template, and vice versa — both updated in the same commit
```

### 3. Always-Run vs. Conditional Pattern

Adopt a consistent inline comment convention in command files:

```markdown
# ALWAYS-RUN: executes on every invocation of this command
- Task icp-researcher(...)

# CONDITIONAL: only runs for event-driven campaigns
- Task event-context-researcher(...)  [if event_date present]
```

The convention makes misclassification visible at code review time without requiring anyone to trace YAML control flow.

### 4. The Planning-Implementation Gap

**The rule:** An agent does not exist until its `.md` file exists. Planning documents may propose agent names, but command files may not reference an agent until the `.md` file has been committed.

The gap widens when:
- One contributor plans, another implements, a third writes command files
- Planning-phase agent names drift from implementation names without reconciliation
- There is no CI check connecting command references to agent files

**The fix:** run the verification command in CI on every pull request, not just at release time. Catching it at PR time means fixing a 2-line grep result; catching it at release time means a shipped bug.

## Related

- [CLAUDE.md pre-commit checklist](../../CLAUDE.md) — versioning and compliance requirements
- [campaign-folders SKILL.md](../../skills/campaign-folders/SKILL.md) — context detection pattern used by commands
- v1.3.0 commit: `f22c07a` — introduced the new agents
- Fix commit: `ac333f5` — resolved all four bugs
