---
name: done
description: >
  Archive completed, skipped, or dropped features and missions to Done.md.
  Browse past completed work and recover deleted mission content via git SHAs.
  Keywords: done, completed, finished, archived, history, shipped, browse
  done, what did we finish, past work, completed features.
user-invocable: false
---

# Done

Archive completed work to Done.md and browse past accomplishments.

## Config Preamble

Before any operation, locate the PlanKit folder and read its config:

1. Check if `PlanKit/` exists in the project root. If not, check `plan-kit/`.
2. Read `.config.json` from the PlanKit folder to get the naming convention.

Determine file path:
- **Done**: `PlanKit/Done.md` or `plan-kit/done.md`

If Done.md doesn't exist, create it with just `# Done` as content.

## Archive Scenarios

Other PlanKit skills trigger these scenarios by loading this skill and specifying which one applies. When loaded, find the matching scenario and follow its instructions.

### Scenario 1: Mission Completed (or Dropped)

Triggered by the `mission-breakdown` skill after a mission file is deleted.

**Input from calling skill:** mission name, mission letter, feature name, feature number, file path, status (✅ completed or 🛑 dropped), reason (for dropped only), PR numbers (if known).

1. **Get the deletion commit SHA**: `git log -1 --format=%h -- "path/to/deleted/file"`
   - This finds the commit that last touched the file (the deletion commit)
2. **Read Done.md**
3. **Find or create the version section**:
   - Look for `## vX.Y` matching the feature's roadmap version
   - If it doesn't exist, create it: `## vX.Y — Subtitle (YYYY-MM-DD →)`
   - Start date = today if this is the first entry for this version
   - End date left blank (filled when version is released)
4. **Find or create the feature subsection**:
   - Look for `### ... NNN-FeatureName` under the version section
   - If it doesn't exist, create it with the feature's one-line description from the Roadmap
   - Don't set the feature-level status marker yet (it's set when the feature is fully complete)
5. **Add the mission line**:
   - Format: `- ✅ X-MissionName · plan: \`SHA\`` (for completed)
   - Format: `- 🛑 X-MissionName · plan: \`SHA\` — reason` (for dropped)
   - Append `· PR #N` if PR numbers were provided
6. **Write Done.md**

### Scenario 2: Feature Completed

Triggered by the `mission-breakdown` skill after all missions are done and the feature folder is deleted.

1. **Read Done.md**
2. **Find the feature subsection** (should already exist from Scenario 1 mission entries)
3. **Set the feature status marker to ✅**: update `###` line to `### ✅ NNN-FeatureName`
4. **Verify all missions are listed** — if any are missing (completed before Done.md existed), add them now
5. **Write Done.md**

### Scenario 3: Feature Skipped or Dropped

Triggered by the `roadmap-planning` skill when a feature is removed from the roadmap without completion.

**Input:** feature name, feature number (if it had one), version, status (⏭️ skipped or 🛑 dropped), reason.

1. **Read Done.md**
2. **Find or create the version section** (same as Scenario 1, step 3)
3. **Add the feature entry**:
   - `### ⏭️ NNN-FeatureName` (skipped — never started) or `### 🛑 NNN-FeatureName` (dropped — started but abandoned)
   - If the feature never got a number (removed during roadmap triage before missions), use just the feature name without a number
   - Next line: the reason, as plain text
4. **Archive any existing missions** — if the feature had mission files, archive each one with its actual status
5. **Write Done.md**

### Scenario 4: Version Released

Triggered by the `roadmap-planning` or `dashboard` skill when a version ships.

**Input:** version number, subtitle, release date, list of features with their final statuses.

1. **Read Done.md**
2. **Find the version section** (should already exist from earlier mission/feature entries)
3. **Fill in the end date**: update `(YYYY-MM-DD →)` to `(YYYY-MM-DD → YYYY-MM-DD)`
4. **Ensure all features have final status markers** (✅, ⏭️, or 🛑)
5. **Add any features that aren't listed yet** (features without missions that were completed directly)
6. **Write Done.md**

## Browse Mode

When the user wants to review completed work (triggered by keywords like "what did we finish", "show history", "browse done"):

1. **Read Done.md**
2. **Present a summary** by version: version name, date range, feature count, status distribution
3. **Offer to show details** for specific versions or features
4. **For recovery**: when user wants to see a deleted mission file, use the `plan:` SHA:
   ```bash
   git show <sha>~1:PlanKit/Features/NNN-FeatureName/X-MissionName.md
   ```
   (Use `~1` to get the parent commit, which still has the file content.)

## Important Rules

- **Done.md is append-mostly** — entries are added as work completes, rarely modified after creation
- **Version sections ordered newest-first** — newest version at the top, oldest at the bottom
- **Git SHAs enable full recovery** — every deleted mission file can be reconstructed from its `plan:` SHA
- **PR references are optional** — include when the user mentions them, skip otherwise. Can be added retroactively.
- **Reason is mandatory for ⏭️ and 🛑** — always explain why something was skipped or dropped
- **One-line descriptions** — feature descriptions are a single sentence from the Roadmap, not the full detail
- **Cross-references use `NNN/X` format** — e.g., `005/A` for feature 005, mission A

## Format Reference

For detailed file format specification, see [DONE_FORMAT.md](references/DONE_FORMAT.md).
