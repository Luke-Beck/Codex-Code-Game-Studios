# Upgrading Codex Code Game Studios

This guide covers upgrading your existing game project repo from one version
of the template to the next.

**Find your current version** in your git log:
```bash
git log --oneline | grep -i "release\|setup"
```
Or check `README.md` for the version badge.

---

## Table of Contents

- [Upgrade Strategies](#upgrade-strategies)
- [v0.2.0 → v0.3.0](#v020--v030)
- [v0.1.0 → v0.2.0](#v010--v020)

---

## Upgrade Strategies

There are three ways to pull in template updates. Choose based on how your
repo is set up.

### Strategy A — Git Remote Merge (recommended)

Best when: you cloned the template and have your own commits on top of it.

```bash
# Add the template as a remote (one-time setup)
git remote add template https://github.com/Donchitos/Codex-Code-Game-Studios.git

# Fetch the new version
git fetch template main

# Merge into your branch
git merge template/main --allow-unrelated-histories
```

Git will flag conflicts only in files that both the template *and* you have
changed. Resolve each one — your game content goes in, structural improvements
come along for the ride. Then commit the merge.

**Tip:** The files most likely to conflict are `AGENTS.md` and
`.codex/docs/technical-preferences.md`, because you've filled them in with
your engine and project settings. Keep your content; accept the structural changes.

---

### Strategy B — Cherry-pick specific commits

Best when: you only want one specific feature (e.g., just the new skill, not
the full update).

```bash
git remote add template https://github.com/Donchitos/Codex-Code-Game-Studios.git
git fetch template main

# Cherry-pick the specific commit(s) you want
git cherry-pick <commit-sha>
```

Commit SHAs for each version are listed in the version sections below.

---

### Strategy C — Manual file copy

Best when: you didn't use git to set up the template (just downloaded a zip).

1. Download or clone the new version alongside your repo.
2. Copy the files listed under **"Safe to overwrite"** directly.
3. For files under **"Merge carefully"**, open both versions side-by-side
   and manually merge the structural changes while keeping your content.

---

## v0.2.0 → v0.3.0

**Released:** 2026-03-09
**Commit range:** `e289ce9..HEAD`
**Key themes:** `/design-system` GDD authoring, `/map-systems` rename, custom status line

### Breaking Changes

#### `/design-systems` renamed to `/map-systems`

The `/design-systems` skill was renamed to `/map-systems` for clarity
(decomposing = *mapping*, not *designing*).

**Action required:** Update any documentation, notes, or scripts that invoke
`/design-systems`. The new invocation is `/map-systems`.

### What Changed

| Category | Changes |
|----------|---------|
| **New skills** | `/design-system` (guided GDD authoring, section-by-section) |
| **Renamed skills** | `/design-systems` → `/map-systems` (breaking rename) |
| **New files** | `.codex/statusline.sh`, `.codex/settings.json` statusline config |
| **Skill updates** | `/gate-check` — writes `production/stage.txt` on PASS, new phase definitions |
| **Skill updates** | `brainstorm`, `start`, `design-review`, `project-stage-detect`, `setup-engine` — cross-reference fixes |
| **Bug fixes** | `log-agent.sh`, `validate-commit.sh` — hook execution fixed |
| **Docs** | `UPGRADING.md` added, `README.md` updated, `WORKFLOW-GUIDE.md` updated |

---

### Files: Safe to Overwrite

**New files to add:**
```
.codex/skills/design-system/SKILL.md
.codex/statusline.sh
```

**Existing files to overwrite (no user content):**
```
.codex/skills/map-systems/SKILL.md      ← was design-systems/SKILL.md
.codex/skills/gate-check/SKILL.md
.codex/skills/brainstorm/SKILL.md
.codex/skills/start/SKILL.md
.codex/skills/design-review/SKILL.md
.codex/skills/project-stage-detect/SKILL.md
.codex/skills/setup-engine/SKILL.md
.codex/hooks/log-agent.sh
.codex/hooks/validate-commit.sh
README.md
docs/WORKFLOW-GUIDE.md
UPGRADING.md
```

**Delete (replaced by rename):**
```
.codex/skills/design-systems/   ← entire directory; replaced by map-systems/
```

---

### Files: Merge Carefully

#### `.codex/settings.json`

The new version adds a `statusLine` configuration block pointing to
`.codex/statusline.sh`. If you haven't customized `settings.json`, overwriting
is safe. Otherwise, add this block manually:

```json
"statusLine": {
  "script": ".codex/statusline.sh"
}
```

---

### New Features

#### Custom Status Line

`.codex/statusline.sh` displays a 7-stage production pipeline breadcrumb in
the terminal status line:

```
ctx: 42% | gpt-5 | Systems Design
```

In Production/Polish/Release stages, it also shows the active Epic/Feature/Task
from `production/session-state/active.md` if a `<!-- STATUS -->` block is present:

```
ctx: 42% | gpt-5 | Production | Combat System > Melee Combat > Hitboxes
```

The current stage is auto-detected from project artifacts, or can be pinned by
writing a stage name to `production/stage.txt`.

#### `/gate-check` Stage Advancement

When a gate PASS verdict is confirmed, `/gate-check` now writes the new stage
name to `production/stage.txt`. This immediately updates the status line for all
future sessions without requiring manual file edits.

---

### After Upgrading

1. **Delete the old skill directory:**
   ```bash
   rm -rf .codex/skills/design-systems/
   ```

2. **Test the status line** by starting a Codex session — you should see
   the stage breadcrumb in the terminal footer.

3. **Verify hook execution** still works:
   ```bash
   bash .codex/hooks/log-agent.sh '{}' '{}'
   bash .codex/hooks/validate-commit.sh '{}' '{}'
   ```

---

## v0.1.0 → v0.2.0

**Released:** 2026-02-21
**Commit range:** `ad540fe..e289ce9`
**Key themes:** Context Resilience, AskUserQuestion integration, `/map-systems` skill

### What Changed

| Category | Changes |
|----------|---------|
| **New skills** | `/start` (onboarding), `/map-systems` (systems decomposition), `/design-system` (guided GDD authoring) |
| **New hooks** | `session-start.sh` (recovery), `detect-gaps.sh` (gap detection) |
| **New templates** | `systems-index.md`, 3 collaborative-protocol templates |
| **Context management** | Major rewrite — file-backed state strategy added |
| **Agent updates** | 14 design/creative agents — AskUserQuestion integration |
| **Skill updates** | All 7 `team-*` skills + `brainstorm` — AskUserQuestion at phase transitions |
| **AGENTS.md** | Slimmed from ~159 to ~60 lines; 5 doc imports instead of 10 |
| **Hook updates** | All 8 hooks — Windows compatibility fixes, new features |
| **Docs removed** | `docs/IMPROVEMENTS-PROPOSAL.md`, `docs/MULTI-STAGE-DOCUMENT-WORKFLOW.md` |

---

### Files: Safe to Overwrite

These are pure infrastructure — you have not customized them. Copy the new
versions directly with no risk to your project content.

**New files to add:**
```
.codex/skills/start/SKILL.md
.codex/skills/map-systems/SKILL.md
.codex/skills/design-system/SKILL.md
.codex/docs/templates/systems-index.md
.codex/docs/templates/collaborative-protocols/design-agent-protocol.md
.codex/docs/templates/collaborative-protocols/implementation-agent-protocol.md
.codex/docs/templates/collaborative-protocols/leadership-agent-protocol.md
.codex/hooks/detect-gaps.sh
.codex/hooks/session-start.sh
production/session-state/.gitkeep
docs/examples/README.md
.github/ISSUE_TEMPLATE/bug_report.md
.github/ISSUE_TEMPLATE/feature_request.md
.github/PULL_REQUEST_TEMPLATE.md
```

**Existing files to overwrite (no user content):**
```
.codex/skills/brainstorm/SKILL.md
.codex/skills/design-review/SKILL.md
.codex/skills/gate-check/SKILL.md
.codex/skills/project-stage-detect/SKILL.md
.codex/skills/setup-engine/SKILL.md
.codex/skills/team-audio/SKILL.md
.codex/skills/team-combat/SKILL.md
.codex/skills/team-level/SKILL.md
.codex/skills/team-narrative/SKILL.md
.codex/skills/team-polish/SKILL.md
.codex/skills/team-release/SKILL.md
.codex/skills/team-ui/SKILL.md
.codex/hooks/log-agent.sh
.codex/hooks/pre-compact.sh
.codex/hooks/session-stop.sh
.codex/hooks/validate-assets.sh
.codex/hooks/validate-commit.sh
.codex/hooks/validate-push.sh
.codex/rules/design-docs.md
.codex/docs/hooks-reference.md
.codex/docs/skills-reference.md
.codex/docs/quick-start.md
.codex/docs/directory-structure.md
.codex/docs/context-management.md
docs/COLLABORATIVE-DESIGN-PRINCIPLE.md
docs/WORKFLOW-GUIDE.md
README.md
```

**Agent files to overwrite** (if you haven't written custom prompts into them):
```
.codex/agents/art-director.md
.codex/agents/audio-director.md
.codex/agents/creative-director.md
.codex/agents/economy-designer.md
.codex/agents/game-designer.md
.codex/agents/level-designer.md
.codex/agents/live-ops-designer.md
.codex/agents/narrative-director.md
.codex/agents/producer.md
.codex/agents/systems-designer.md
.codex/agents/technical-director.md
.codex/agents/ux-designer.md
.codex/agents/world-builder.md
.codex/agents/writer.md
```

If you *have* customized agent prompts, see "Merge carefully" below.

---

### Files: Merge Carefully

These files contain both template structure and your project-specific content.
Do **not** overwrite them — merge the changes manually.

#### `AGENTS.md`

The template version was slimmed from ~159 lines to ~60 lines. The key
structural change: 5 doc imports were removed because they're auto-loaded
by Codex anyway (agent-roster, skills-reference, hooks-reference,
rules-reference, review-workflow).

**What to keep from your version:**
- The `## Technology Stack` section (your engine/language choices)
- Any project-specific additions you made

**What to adopt from the new version:**
- Slimmer imports list (drop the 5 redundant `@` imports if present)
- Updated collaboration protocol wording

#### `.codex/docs/technical-preferences.md`

If you ran `/setup-engine`, this file has your engine config, naming
conventions, and performance budgets. Keep all of it. The template version
is just the empty placeholder.

#### `.codex/docs/templates/game-concept.md`

Minor structural update — a `## Next Steps` section was added pointing to
`/map-systems`. Add that section to your copy if you want the updated
guidance, but it's not required.

#### `.codex/settings.json`

Check whether the new version adds any permission rules you want. The change
was minor (schema update). If you haven't customized your `settings.json`,
overwriting is safe.

#### Customized agent files

If you've added project-specific knowledge or custom behavior to any agent
`.md` file, do a diff and manually add the new AskUserQuestion integration
sections rather than overwriting. The change in each agent is a standardized
collaborative protocol block at the end of the system prompt.

---

### Files: Delete

These files were removed in v0.2.0. If present in your repo, you can safely
delete them — they're replaced by better-organized alternatives.

```
docs/IMPROVEMENTS-PROPOSAL.md      → superseded by WORKFLOW-GUIDE.md
docs/MULTI-STAGE-DOCUMENT-WORKFLOW.md → content merged into context-management.md
```

---

### After Upgrading

1. **Run `/project-stage-detect`** to verify the system reads your project
   correctly with the new detection logic.

2. **Run `/start`** once if you haven't used it — it now correctly identifies
   your stage and skips onboarding steps you've already done.

3. **Check `production/session-state/`** exists and is gitignored:
   ```bash
   ls production/session-state/
   cat .gitignore | grep session-state
   ```

4. **Test hook execution** — if you're on Windows, verify the new hooks run
   without errors in Git Bash:
   ```bash
   bash .codex/hooks/detect-gaps.sh '{}' '{}'
   bash .codex/hooks/session-start.sh '{}' '{}'
   ```

---

*Each future version will have its own section in this file.*
