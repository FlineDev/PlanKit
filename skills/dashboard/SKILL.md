---
name: dashboard
user-invocable: false
description: >
  Project dashboard that tracks progress on the current version. Only
  activates in projects with a PlanKit folder. Triggers automatically
  when the user asks about status or progress, mentions completing a
  mission, or discusses shipping a version. Keywords: progress, status,
  what's done, where are we, show progress, how far, what's left,
  current status, mark done, mark complete, ship version, release
  version, what did we finish, mission is done, just finished, we released,
  version is out.
---

# Dashboard

Automatic project dashboard — tracks feature progress, mission completion, and version releases via Progress.md.

This skill has **no command**. It triggers automatically when the user discusses status, completion, or releases. Other PlanKit skills (plan-roadmap, detail-missions) also update Progress.md directly as part of their workflows.

## Config Preamble

Before any operation, locate the PlanKit folder and read its config:

1. Check if `PlanKit/` exists in the project root. If not, check `plan-kit/`.
2. If neither exists, tell the user: "No PlanKit folder found. Run `/plan-kit:init` to set up PlanKit first."
3. Read `.config.json` from the PlanKit folder to get the naming convention.

Determine file paths:
- **Progress**: `PlanKit/Progress.md` or `plan-kit/progress.md`
- **Features dir**: `PlanKit/Features/` or `plan-kit/features/`
- **Roadmap**: `PlanKit/Roadmap.md` or split `PlanKit/Roadmap/`
  - Or: `plan-kit/roadmap.md` or split `plan-kit/roadmap/`

## View Dashboard

When the user asks about status/progress:

1. **Read Progress.md** — if it doesn't exist, tell the user: "No Progress.md yet. It's created when you plan your first version with `/plan-kit:plan-roadmap`."
2. **Display a visual summary** in chat:

   ```
   ═══════════════════════════════════════════════════
   📊 DASHBOARD — vX.Y — Version Subtitle
   ═══════════════════════════════════════════════════

   Feature Name                          [▓▓▓▓░░░░] 2/4
     ✅ Mission One
     ▶  Mission Two (current)
     ○  Mission Three
     ○  Mission Four

   Another Feature                       Planned
     (No missions yet)

   Third Feature                         [▓▓▓▓▓▓▓▓] Done
     ✅ Only Mission

   ───────────────────────────────────────────────────
   Overall: 3/5 missions done · 1 in progress · 1 planned
   ═══════════════════════════════════════════════════
   ```

3. **Offer actions** if there's something actionable:
   - If a mission is marked `[>]`, mention it's the current focus
   - If all missions for a feature are `[x]`, suggest marking the feature as Done
   - If all features are Done, suggest releasing the version

## Update Mission Markers

When the user says a mission is done, in progress, or wants to change status:

1. **Read Progress.md**
2. **Find the mission** by name match
3. **Update the marker**:
   - `[ ]` → `[>]` when starting a mission (mark as current)
   - `[>]` → `[x]` when completing a mission
   - `[ ]` → `[x]` when completing a mission directly
4. **Check feature completion**: if all missions for a feature are `[x]`:
   - Update the feature's `Status: In Progress` → `Status: Done`
5. **Write updated Progress.md**
6. **Confirm** the change briefly

## Version Release

When all features are Done or user says the version shipped:

1. **Read Progress.md**
2. **Slim the Current section** — move completed features to `## Released`:
   - Add a new entry under Released: `### vX.Y — Subtitle (YYYY-MM-DD)` with bullet list of feature names
   - Remove the detailed feature sections from Current
3. **Handle incomplete features**:
   - Any features with `Status: Planned` or `Status: In Progress` → carry over to a new `## Current:` section for the next version
   - Use AskUserQuestion to confirm the next version number
4. **Write updated Progress.md**
5. **Archive to Done.md** — load the `done` skill and follow its **Scenario 4: Version Released** instructions
6. **Confirm** what was released and what carries over

## Recover Deleted Mission Content

When a mission file has been deleted but the user needs its content:

1. **Check Done.md first** — look for the mission's `plan:` SHA reference. If found, use it directly: `git show <sha>~1:path/to/file`
2. **Fallback to git search**: `git log --all --full-history -- "path/to/file"` then `git show <commit>:path/to/file`
3. **Find the file path** from the Progress.md mission line (in backticks) or Done.md mission line
4. **Display the content** to the user
5. **Offer to recreate** the file if needed

## Important Rules

- **No command** — this skill triggers automatically from conversation context
- **One Current version at a time** — indie devs work on one version; Progress.md has exactly one `## Current:` section
- **Mission markers are authoritative** — `[ ]` not started, `[>]` in progress, `[x]` done
- **File paths on every mission line** — preserved in backticks for git recovery after deletion
- **Feature status derives from missions** — `Planned` (no missions or all `[ ]`), `In Progress` (any `[>]` or mix of `[x]`/`[ ]`), `Done` (all `[x]`)
- **Released section is slim** — just version + date + feature name bullets, no mission details
- **Progress.md is created lazily** — by the plan-roadmap skill when a version is first planned

## Format Reference

For detailed file format specification, see [DASHBOARD_FORMAT.md](references/DASHBOARD_FORMAT.md).
